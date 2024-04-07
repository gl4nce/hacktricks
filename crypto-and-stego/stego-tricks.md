# Sztuczki Stego

<details>

<summary><strong>Nauka hakowania AWS od zera do bohatera z</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong>!</strong></summary>

Inne sposoby wsparcia HackTricks:

* Jeśli chcesz zobaczyć swoją **firmę reklamowaną w HackTricks** lub **pobrać HackTricks w formacie PDF**, sprawdź [**PLANY SUBSKRYPCYJNE**](https://github.com/sponsors/carlospolop)!
* Kup [**oficjalne gadżety PEASS & HackTricks**](https://peass.creator-spring.com)
* Odkryj [**Rodzinę PEASS**](https://opensea.io/collection/the-peass-family), naszą kolekcję ekskluzywnych [**NFT**](https://opensea.io/collection/the-peass-family)
* **Dołącz do** 💬 [**grupy Discord**](https://discord.gg/hRep4RUj7f) lub [**grupy telegramowej**](https://t.me/peass) lub **śledź** nas na **Twitterze** 🐦 [**@carlospolopm**](https://twitter.com/hacktricks\_live)**.**
* **Podziel się swoimi sztuczkami hakowania, przesyłając PR-y do** [**HackTricks**](https://github.com/carlospolop/hacktricks) i [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) na GitHubie.

</details>

**Grupa Try Hard Security**

<figure><img src="/.gitbook/assets/telegram-cloud-document-1-5159108904864449420.jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://discord.gg/tryhardsecurity" %}

***

## **Wyciąganie Danych z Plików**

### **Binwalk**

Narzędzie do wyszukiwania ukrytych plików i danych osadzonych w plikach binarnych. Instalowane za pomocą `apt`, a jego źródło jest dostępne na [GitHub](https://github.com/ReFirmLabs/binwalk).
```bash
binwalk file # Displays the embedded data
binwalk -e file # Extracts the data
binwalk --dd ".*" file # Extracts all data
```
### **Foremost**

Odzyskuje pliki na podstawie ich nagłówków i stóp, przydatne dla obrazów png. Zainstaluj za pomocą `apt` z kodem źródłowym na [GitHub](https://github.com/korczis/foremost).
```bash
foremost -i file # Extracts data
```
### **Exiftool**

Pomaga wyświetlać metadane pliku, dostępny [tutaj](https://www.sno.phy.queensu.ca/\~phil/exiftool/).
```bash
exiftool file # Shows the metadata
```
### **Exiv2**

Podobnie jak exiftool, do przeglądania metadanych. Można zainstalować za pomocą `apt`, źródło na [GitHub](https://github.com/Exiv2/exiv2), oraz posiada [oficjalną stronę internetową](http://www.exiv2.org/).
```bash
exiv2 file # Shows the metadata
```
### **Plik**

Zidentyfikuj rodzaj pliku, z którym masz do czynienia.

### **Ciągi znaków**

Wyodrębnia czytelne ciągi znaków z plików, używając różnych ustawień kodowania do filtrowania wyników.
```bash
strings -n 6 file # Extracts strings with a minimum length of 6
strings -n 6 file | head -n 20 # First 20 strings
strings -n 6 file | tail -n 20 # Last 20 strings
strings -e s -n 6 file # 7bit strings
strings -e S -n 6 file # 8bit strings
strings -e l -n 6 file # 16bit strings (little-endian)
strings -e b -n 6 file # 16bit strings (big-endian)
strings -e L -n 6 file # 32bit strings (little-endian)
strings -e B -n 6 file # 32bit strings (big-endian)
```
### **Porównanie (cmp)**

Przydatne do porównywania zmodyfikowanego pliku z jego oryginalną wersją znalezioną online.
```bash
cmp original.jpg stego.jpg -b -l
```
## **Wyciąganie Ukrytych Danych z Tekstu**

### **Ukryte Dane w Spacjach**

Niewidoczne znaki w pozornie pustych miejscach mogą zawierać informacje. Aby wydobyć te dane, odwiedź [https://www.irongeek.com/i.php?page=security/unicode-steganography-homoglyph-encoder](https://www.irongeek.com/i.php?page=security/unicode-steganography-homoglyph-encoder).

## **Wyciąganie Danych z Obrazów**

### **Identyfikacja Szczegółów Obrazu za Pomocą GraphicMagick**

[GraphicMagick](https://imagemagick.org/script/download.php) służy do określania typów plików obrazów i identyfikowania potencjalnych uszkodzeń. Wykonaj poniższą komendę, aby przeanalizować obraz:
```bash
./magick identify -verbose stego.jpg
```
Aby spróbować naprawić uszkodzony obraz, dodanie komentarza metadanych może pomóc:
```bash
./magick mogrify -set comment 'Extraneous bytes removed' stego.jpg
```
### **Steghide do ukrywania danych**

Steghide ułatwia ukrywanie danych w plikach `JPEG, BMP, WAV i AU`, zdolny do osadzania i wydobywania zaszyfrowanych danych. Instalacja jest prosta za pomocą `apt`, a jego [kod źródłowy jest dostępny na GitHubie](https://github.com/StefanoDeVuono/steghide).

**Polecenia:**

* `steghide info plik` ujawnia, czy plik zawiera ukryte dane.
* `steghide extract -sf plik [--hasło hasło]` wydobywa ukryte dane, hasło opcjonalne.

Dla wydobycia danych za pomocą przeglądarki, odwiedź [tę stronę internetową](https://futureboy.us/stegano/decinput.html).

**Atak brutalnej siły z użyciem Stegcrackera:**

* Aby spróbować złamać hasło w Steghide, użyj [stegcrackera](https://github.com/Paradoxis/StegCracker.git) w następujący sposób:
```bash
stegcracker <file> [<wordlist>]
```
### **zsteg dla plików PNG i BMP**

zsteg specjalizuje się w odkrywaniu ukrytych danych w plikach PNG i BMP. Instalacja odbywa się za pomocą `gem install zsteg`, a jego [źródło znajduje się na GitHubie](https://github.com/zed-0xff/zsteg).

**Polecenia:**

* `zsteg -a plik` stosuje wszystkie metody wykrywania na pliku.
* `zsteg -E plik` określa ładunek do ekstrakcji danych.

### **StegoVeritas i Stegsolve**

**stegoVeritas** sprawdza metadane, wykonuje transformacje obrazu, stosuje siłowe łamanie LSB i inne funkcje. Użyj `stegoveritas.py -h` aby uzyskać pełną listę opcji oraz `stegoveritas.py stego.jpg` aby wykonać wszystkie sprawdzenia.

**Stegsolve** stosuje różne filtry kolorów, aby ujawnić ukryte teksty lub wiadomości w obrazach. Jest dostępny na [GitHubie](https://github.com/eugenekolo/sec-tools/tree/master/stego/stegsolve/stegsolve).

### **FFT do Wykrywania Ukrytej Zawartości**

Techniki Szybkiej Transformaty Fouriera (FFT) mogą ujawnić ukrytą zawartość w obrazach. Przydatne zasoby to:

* [Demo EPFL](http://bigwww.epfl.ch/demo/ip/demos/FFT/)
* [Ejectamenta](https://www.ejectamenta.com/Fourifier-fullscreen/)
* [FFTStegPic na GitHubie](https://github.com/0xcomposure/FFTStegPic)

### **Stegpy dla Plików Audio i Obrazów**

Stegpy pozwala na osadzanie informacji w plikach obrazów i dźwięku, obsługując formaty takie jak PNG, BMP, GIF, WebP i WAV. Jest dostępny na [GitHubie](https://github.com/dhsdshdhk/stegpy).

### **Pngcheck do Analizy Plików PNG**
```bash
apt-get install pngcheck
pngcheck stego.png
```
### **Dodatkowe narzędzia do analizy obrazów**

Dla dalszego zgłębiania tematu, rozważ odwiedzenie:

* [Magic Eye Solver](http://magiceye.ecksdee.co.uk/)
* [Analiza poziomu błędów obrazu](https://29a.ch/sandbox/2012/imageerrorlevelanalysis/)
* [Outguess](https://github.com/resurrecting-open-source-projects/outguess)
* [OpenStego](https://www.openstego.com/)
* [DIIT](https://diit.sourceforge.net/)

## **Wyciąganie danych z plików dźwiękowych**

**Steganografia dźwiękowa** oferuje unikalną metodę ukrywania informacji w plikach dźwiękowych. Do osadzania lub odzyskiwania ukrytej zawartości wykorzystuje się różne narzędzia.

### **Steghide (JPEG, BMP, WAV, AU)**

Steghide to wszechstronne narzędzie przeznaczone do ukrywania danych w plikach JPEG, BMP, WAV i AU. Szczegółowe instrukcje znajdują się w [dokumentacji sztuczek steganograficznych](stego-tricks.md#steghide).

### **Stegpy (PNG, BMP, GIF, WebP, WAV)**

To narzędzie jest kompatybilne z różnymi formatami, w tym PNG, BMP, GIF, WebP i WAV. Aby uzyskać więcej informacji, zajrzyj do [sekcji Stegpy](stego-tricks.md#stegpy-png-bmp-gif-webp-wav).

### **ffmpeg**

ffmpeg jest niezbędny do oceny integralności plików dźwiękowych, podkreślając szczegółowe informacje i wskazując wszelkie niezgodności.
```bash
ffmpeg -v info -i stego.mp3 -f null -
```
### **WavSteg (WAV)**

WavSteg doskonale ukrywa i wydobywa dane w plikach WAV, używając strategii najmniej znaczącego bitu. Jest dostępny na [GitHub](https://github.com/ragibson/Steganography#WavSteg). Polecenia obejmują:
```bash
python3 WavSteg.py -r -b 1 -s soundfile -o outputfile

python3 WavSteg.py -r -b 2 -s soundfile -o outputfile
```
### **Deepsound**

Deepsound umożliwia szyfrowanie i wykrywanie informacji w plikach dźwiękowych za pomocą AES-256. Można go pobrać ze [strony oficjalnej](http://jpinsoft.net/deepsound/download.aspx).

### **Sonic Visualizer**

Niezastąpione narzędzie do wizualnej i analitycznej inspekcji plików dźwiękowych, Sonic Visualizer może ujawnić ukryte elementy niewykrywalne innymi środkami. Odwiedź [oficjalną stronę internetową](https://www.sonicvisualiser.org/) po więcej informacji.

### **DTMF Tones - Sygnały wybierania**

Wykrywanie sygnałów DTMF w plikach dźwiękowych można osiągnąć za pomocą narzędzi online, takich jak [ten detektor DTMF](https://unframework.github.io/dtmf-detect/) i [DialABC](http://dialabc.com/sound/detect/index.html).

## **Inne Techniki**

### **Długość Binarna SQRT - Kod QR**

Dane binarne, które dają liczbę całkowitą po podniesieniu do kwadratu, mogą reprezentować kod QR. Skorzystaj z tego fragmentu, aby sprawdzić:
```python
import math
math.sqrt(2500) #50
```
### **Tłumaczenie na język polski**

Do konwersji z binarnego na obraz, sprawdź [dcode](https://www.dcode.fr/binary-image). Aby odczytać kody QR, skorzystaj z [tego czytnika kodów kreskowych online](https://online-barcode-reader.inliteresearch.com/).

### **Tłumaczenie na alfabet Braille'a**

Do tłumaczenia na alfabet Braille'a, [Tłumacz Braille'a Branah](https://www.branah.com/braille-translator) to doskonałe narzędzie.

## **Referencje**

* [**https://0xrick.github.io/lists/stego/**](https://0xrick.github.io/lists/stego/)
* [**https://github.com/DominicBreuker/stego-toolkit**](https://github.com/DominicBreuker/stego-toolkit)

**Grupa Try Hard Security**

<figure><img src="/.gitbook/assets/telegram-cloud-document-1-5159108904864449420.jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://discord.gg/tryhardsecurity" %}

<details>

<summary><strong>Zacznij od zera i zostań ekspertem w hakowaniu AWS z</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong>!</strong></summary>

Inne sposoby wsparcia HackTricks:

* Jeśli chcesz zobaczyć swoją **firmę reklamowaną w HackTricks** lub **pobrać HackTricks w formacie PDF**, sprawdź [**PLANY SUBSKRYPCYJNE**](https://github.com/sponsors/carlospolop)!
* Zdobądź [**oficjalne gadżety PEASS & HackTricks**](https://peass.creator-spring.com)
* Odkryj [**Rodzinę PEASS**](https://opensea.io/collection/the-peass-family), naszą kolekcję ekskluzywnych [**NFT**](https://opensea.io/collection/the-peass-family)
* **Dołącz do** 💬 [**grupy Discord**](https://discord.gg/hRep4RUj7f) lub [**grupy telegramowej**](https://t.me/peass) lub **śledź** nas na **Twitterze** 🐦 [**@carlospolopm**](https://twitter.com/hacktricks\_live)**.**
* **Podziel się swoimi sztuczkami hakerskimi, przesyłając PR-y do** [**HackTricks**](https://github.com/carlospolop/hacktricks) i [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) github repos.

</details>