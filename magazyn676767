import streamlit as st

# --- Konfiguracja Strony ---
st.set_page_config(
    page_title="Prosty Magazyn Towarów",
    layout="centered"
)

# --- Inicjalizacja Stanu Sesji ---
# Lista towarów jest przechowywana w st.session_state
if 'towary' not in st.session_state:
    st.session_state.towary = [
        {"nazwa": "Laptop Dell XYZ", "ilosc": 5, "lokalizacja": "A-01"},
        {"nazwa": "Monitor LG 27'", "ilosc": 12, "lokalizacja": "B-03"},
        {"nazwa": "Myszka bezprzewodowa", "ilosc": 30, "lokalizacja": "C-10"},
    ]

## --- Funkcje Logiki Magazynu ---

def dodaj_towar(nazwa, ilosc, lokalizacja):
    """Dodaje nowy towar do listy."""
    try:
        ilosc_int = int(ilosc)
        if ilosc_int <= 0:
            st.error("Ilość musi być liczbą całkowitą większą od zera.")
            return
    except ValueError:
        st.error("Ilość musi być liczbą całkowitą.")
        return

    nowy_towar = {
        "nazwa": nazwa,
        "ilosc": ilosc_int,
        "lokalizacja": lokalizacja
    }
    st.session_state.towary.append(nowy_towar)
    st.success(f"Dodano towar: **{nazwa}**")

def usun_towar(index):
    """Usuwa towar z listy na podstawie indeksu."""
    if 0 <= index < len(st.session_state.towary):
        nazwa_usunieta = st.session_state.towary[index]['nazwa']
        del st.session_state.towary[index]
        st.warning(f"Usunięto towar: **{nazwa_usunieta}**")
    else:
        st.error("Błąd: Nieprawidłowy indeks towaru.")

# --- Interfejs Użytkownika ---

st.title("📦 Prosty Magazyn Towarów (Streamlit)")
st.caption("Dane są przechowywane tylko w pamięci aplikacji i **nie są zapisywane** na stałe.")

## 1. Wyświetlanie Magazynu
st.header("Stan Magazynu")

if st.session_state.towary:
    # Używamy pandas DataFrame dla ładniejszej tabeli
    import pandas as pd
    df = pd.DataFrame(st.session_state.towary)
    
    # Dodajemy kolumnę dla przycisków usuwania
    df['Usuń'] = [False] * len(df)
    
    # Wyświetlamy edytowalną tabelę Streamlit
    edited_df = st.data_editor(
        df,
        column_config={
            "nazwa": "Nazwa Towaru",
            "ilosc": st.column_config.NumberColumn("Ilość", format="%d", help="Liczba sztuk w magazynie"),
            "lokalizacja": "Lokalizacja",
            "Usuń": st.column_config.CheckboxColumn("Usuń", help="Zaznacz, aby usunąć towar", default=False)
        },
        disabled=("nazwa", "ilosc", "lokalizacja"), # Zezwalamy na edycję tylko kolumny "Usuń"
        hide_index=True,
        key="magazyn_data_editor"
    )

    # Logika usuwania: sprawdzamy, które wiersze zostały zaznaczone do usunięcia
    indeksy_do_usunięcia = edited_df[edited_df['Usuń'] == True].index.tolist()
    
    if indeksy_do_usunięcia:
        # Sortujemy indeksy malejąco, aby usunięcie jednego nie wpływało na indeksy kolejnych
        indeksy_do_usunięcia.sort(reverse=True)
        
        for index in indeksy_do_usunięcia:
            # Musimy użyć indeksu z oryginalnej listy (st.session_state.towary), 
            # ponieważ data_editor zwraca tylko to, co zostało edytowane.
            # W tym prostym przypadku indeksy się pokrywają, jeśli nic nie było sortowane.
            # Bardziej złożona logika wymagałaby użycia unikalnego ID.
            usun_towar(index)
        
        # Po usunięciu trzeba odświeżyć aplikację
        st.experimental_rerun()

else:
    st.info("Magazyn jest pusty.")

st.markdown("---")

## 2. Dodawanie Nowego Towaru
st.header("Dodaj Nowy Towar")

with st.form(key='dodaj_towar_form'):
    nowa_nazwa = st.text_input("Nazwa Towaru:")
    nowa_ilosc = st.number_input("Ilość:", min_value=1, step=1, value=1)
    nowa_lokalizacja = st.text_input("Lokalizacja (np. A-05):")
    
    submit_button = st.form_submit_button(label='➕ Dodaj do Magazynu')
    
    if submit_button:
        if nowa_nazwa and nowa_lokalizacja:
            dodaj_towar(nowa_nazwa, nowa_ilosc, nowa_lokalizacja)
            # Po dodaniu można odświeżyć formularz (opcjonalnie)
            # st.experimental_rerun()
        else:
            st.error("Proszę wypełnić pola 'Nazwa Towaru' i 'Lokalizacja'.")
