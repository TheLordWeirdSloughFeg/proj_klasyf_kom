# proj_klasyf_kom
Klasyfikacja komentarzy internetowych dla sklepu Amazon
![Amazon](https://github.com/TheLordWeirdSloughFeg/proj_klasyf_kom/blob/main/Sentiment%20Analysis%20of%20Amazon%20Reviews.jpg)

Projekt w MS Azure


[Link do eksperymentu](https://gallery.cortanaintelligence.com/Experiment/Klasyfikacja-komentarzy-internetowych-dla-sklepu-Amazon)


## 1. Zrozumienie uwarunkowań biznesowych
Firma Amazon posiada największy na świecie sklep internetowy. Co roku baza produktów się rozrasta, a co za tym idzie, stanowi wyzwanie dla osób zarządzających katalogiem produktów. W trosce o utrzymanie jakości co do zamieszczanych produktów Amazon potrzebuje analizy komentarzy użytkowników, aby dopasować swoją ofertę do wymagań klientów.<br />
Plan to wdrożenie odpowiednich metod uczenia maszynowego na platformie MS Azure, w celu weryfikacji komentarzy użytkowników i podzielenie ich na dwie klasy: komentarze pozytywne oraz negatywne.

## 2. Zrozumienie danych 
Mój zleceniodawca – firma Amazon posiada stale rozrastającą się bazę klientów, którzy oceniają swoje zakupy w sklepie. Około 3-5% osób zostawia komentarz – recenzję produktu na stronie. Mimo niewielkiego odsetku recenzujących, co godzinę sklep sprzedaje około 700.000 przedmiotów. Z uwagi na to i na rosnące zainteresowanie klientów ilość wystawianych komentarzy ograniczono do maksymalnie 5 komentarzy tygodniowo, aby zaoszczędzić pamięć serwerową.<br />
Pracownicy Amazona dostarczyli mi komentarzy ocenionych i zweryfikowanych jako 500 pozytywne oraz 500 komentarzy ocenionych jako negatywne. Wybrane komentarze do potrzeb badań zostały losowo wybrane z ogromnej puli recenzji użytkowników.<br />
Dane składają się z komentarza (dane tekstowe) oraz określenia czy komentarz jest pozytywny (1) czy negatywny (0). Zamiarem firmy jest znalezienie i oznaczanie komentarzy w taki sposób, bez puli recenzji neutralnych.

## 3. Przygotowanie danych 
Otrzymałem od firmy dane w pliku .txt bez nazw kolumn. Kolumna oznaczająca binarnie komentarz była oddzielona tabulatorem od komentarzy w formie tekstu, co nie nadawało się do analizy. Za pomocą zwykłego notatnika zamieniłem przecinki na spacje oraz tabulatory na przecinki, aby oddzielić od siebie kolumny. Cały plik skonwertowałem do pliku csv, który wrzuciłem na platformę MS Azure. W kolumnie z oceną (Col 2) brakowało danych, dlatego w następnej kolejności oczyściłem dane z nieocenionych komentarzy. W kolejnym kroku kolumnę z oceną zmieniłem jako kolumnę kategoryczną. Następnie dla ułatwienia zamieniam wartości 0 oraz 1 na odpowiednio „negative” oraz „positive”. Dla bezpieczeństwa dodaję jeszcze etykietę „default” gdyby kolumna 2 miała jeszcze jakieś inne wartości. Tak przygotowane dane są gotowe do modelowania.

## 4. Modelowanie
Ponieważ problem dotyczy analizy języka naturalnego (NLP), potrzebny mi był algorytm, który niejako rozumie i przetwarza tekst napisany przez użytkowników. Zakłada on na wstępne przetwarzanie tekstu, przez usunięcie słów występujących najczęściej (w przypadku języka angielskiego: „a”, „an”, „the”, „at” itp. ), a następnie dokonanie lematyzacji, czyli sprowadzenie danego słowa do jego formy podstawowej (bez oboczności). Wszelkie hiperłącza oraz adresy mailowe również muszą być usunięte, gdyż nie stanowią one wartości dla komentarza. Kolejnym z ważnych etapów jest usunięcie powtarzających się liter, znaków specjalnych i znormalizowanie tekstu do małych liter.<br />
Tak przygotowane dane rozdzielam na dwie części: 40% danych będzie danymi treningowymi modelu, a 60% danych będzie testowana przez mój model.
Przed zastosowaniem modelu uczenia maszynowego z komentarzy utworzyłem n-gramy (dokładnie trigramy), aby model był w stanie wychwytywać najczęściej pojawiające się frazy. Minimalna długość słowa to 3 litery a maksymalna to 25 liter. Minimalna częstość występowania trigramu w utworzonym słowniku to 5.<br />
Zdecydowałem się na zastosowanie algorytmu dwuklasowej regresji logistycznej, z uwagi iż jest to problem klasyfikacji binarnej. Regresja logistyczna jest podobna do modelu regresji liniowej, ale nadaje się dla modeli, w których zmienna zależna jest dychotomiczna. Model regresji logistycznej oparty jest na funkcji przekształcającej prawdopodobieństwo na logarytm szansy zwany inaczej logitem, co pozwala na obliczanie prawdopodobieństwa danego zdarzenia (tzw. prawdopodobieństwo sukcesu). W przypadku regresji logistycznej współczynniki mogą być używane do oszacowania ilorazów szans dla każdej zmiennej niezależnej w modelu.<br />
Tak przygotowany model dwuklasowej regresji logistycznej został wytrenowany, a następnie został poddany ocenie, na podstawie zbudowanej listy n-gramów. Na koniec wykreśliłem krzywą ROC oraz obliczyłem współczynniki: Accuracy, Precision, Recall i F1 Score.

## 5. Ewaluacja
Otrzymane współczynniki oraz krzywą ROC umieściłem poniżej.

<div align="center">
  
| Parametr | Wartość |
| ----------- | ----------- |
| Accuracy | 0.726 |
| Precision |  0.690 |
| Recall | 0.819 |
| F1 Score | 0.749 |

</div>

<p align="center">
  <img src="https://github.com/TheLordWeirdSloughFeg/proj_klasyf_kom/blob/main/roc_azure.jpg" />
</p>
<br />
<p align="center">
  <b><font size="12">Rys. 1 – Krzywa ROC modelu regresji logistycznej</font></b>
</p>

Współczynnik AUC wyniósł 0.771, co oznacza, że model będzie w stanie w większości przewidzieć czy komentarz jest pozytywny, czy negatywny (w ok. 77% przypadków). Patrząc na Rysunek 1, można zauważyć, że model trochę gorzej radzi sobie z oceną komentarzy pozytywnych, niż negatywnych (więcej komentarzy ocenionych fałszywie pozytywnie).<br />
Z perspektywy firmy, najbardziej chcieliby się pozbyć przedmiotów, które nie zadowalają klientów, gdyż takie przedmioty mogą wpłynąć na negatywny odbiór firmy. Na lepszy wynik mógłby wpłynąć większy, bardziej zróżnicowany zestaw danych, jednak firma zapewniła, że przekaże więcej danych po obiecujących rezultatach na obecnym zbiorze danych.

## 6. Wdrożenie
Model na podstawie regresji logistycznej, oceniający komentarzy na podstawie analizy języka naturalnego (NLP) został przetestowany i działa poprawnie. W kolejnym kroku, przed jego implementacją, należy sprawdzić jak radzi sobie z dużymi zbiorami danych, gdyż w takich warunkach musi funkcjonować. Na koniec można go włączyć w strukturę serwisu Amazon. Taka analiza komentarzy klientów pozwoli firmie Amazon prześledzić również trendy zakupowe i dostosować swój katalog produktów do potrzeb użytkowników sklepu. Z czasem warto byłoby zmodyfikować model tak, aby rozróżniał także komentarze neutralne, a także połączyć go z innymi algorytmami, np. algorytmami pozycjonującymi przedmioty, prezentując najbardziej korzystne i atrakcyjne oferty.
