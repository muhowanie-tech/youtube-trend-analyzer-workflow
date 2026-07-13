# YouTube Trend Analyzer & AI Content Strategist

Automatyczny workflow zbudowany w **n8n**, który codziennie skanuje YouTube w poszukiwaniu viralowych filmów w wybranych kategoriach, ocenia je własnym algorytmem rankingowym, pobiera najlepsze materiały, analizuje je multimodalnie przy pomocy AI i wysyła gotowy raport strategiczny na Telegram — w pełni bez udziału człowieka.

## Jak to działa

Pipeline składa się z czterech etapów:

### 1. Pobieranie danych
Schedule Trigger uruchamia workflow cyklicznie. Zestaw predefiniowanych zapytań (kategorie tematyczne) trafia do YouTube Data API v3 (`search.list`), które zwraca filmy opublikowane w ostatnich 14 dniach dla wybranego regionu. Dla każdego filmu pobierane są dodatkowo statystyki (`videos.list`): wyświetlenia, polubienia, komentarze.

### 2. Ranking
Autorski algorytm scoringu ocenia każdy film na podstawie ważonej, capowanej sumy trzech metryk (wyświetlenia, polubienia, komentarze), tak by żadna z nich nie zdominowała wyniku. Filmy są sortowane malejąco po score, a do dalszego przetwarzania trafia top N.

### 3. Przetwarzanie pliku
Wybrane filmy są pobierane lokalnie (`yt-dlp`), odczytywane z dysku i wysyłane jako pliki binarne do Google Gemini Files API — do dalszej analizy multimodalnej (nie tylko na podstawie metadanych, ale realnej treści wideo).

### 4. Analiza AI i wysyłka
Dwuetapowy pipeline AI:
- **Gemini 2.5 Flash** — analizuje treść wideo według ustrukturyzowanego frameworku (Hak, Problem, Logika, Emocje, Wiarygodność, Struktura, elementy Powtarzalne) zdefiniowanego w systemowym prompcie.
- **GPT** — filtruje i redaguje wyniki, odrzuca materiały nieistotne, kondensuje analizę do zwięzłego raportu.

Gotowy raport trafia automatycznie na Telegram.

## Stack technologiczny

- **n8n** — orkiestracja workflow
- **YouTube Data API v3** — wyszukiwanie i statystyki filmów
- **yt-dlp** — pobieranie plików wideo
- **Google Gemini API** — multimodalna analiza treści wideo
- **OpenAI API** — redakcja i filtrowanie wyników
- **Telegram Bot API** — dystrybucja raportu
- **JavaScript (n8n Code nodes)** — logika scoringu, transformacja danych

## Co warto podkreślić

- Multimodalna analiza wideo AI (model realnie analizuje plik wideo, nie tylko opis/metadane).
- Dwuetapowy pipeline AI z podziałem ról (ekstrakcja vs. redakcja) — świadome zarządzanie jakością i kosztem.
- Autorski algorytm rankingujący zamiast prostego sortowania po popularności.
- Prompt engineering wysokiego poziomu — wymuszony, powtarzalny framework analizy.
- Pełna automatyzacja end-to-end: od odkrycia trendu po dystrybucję gotowego raportu.

## Uwaga

Plik workflow (`youtube-trend-analyzer-workflow-public.json`) ma usunięte klucze API (zastąpione placeholderami) — do uruchomienia wymagane są własne dane uwierzytelniające (YouTube Data API, Gemini, OpenAI, Telegram).
