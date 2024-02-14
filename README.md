
# Volatility Tutorial
1. Om te kunnen weten met welke image je werkt, en wat de profile is, type: `volatility -f <file> imageinfo`
2. Het profiel staat weergeven onder `Suggested Profile(s)`
3. Om vervolgens een `kdbgscan` uit te voeren, type: `volatility -f <file> --profile=<profile> kdbgscan`
4. Om te kunnen weten welke processen actief ware, type: `volatility -f <file> --profile=<profile> pslist`
5. Om te kunnen weten welke verstopte processen actief waren, type `volatility -f <file> --profile=<profile> pstree`
6. Als we we willen zien wat een process deed, dan typen we `volatility -f <file> --profile=<profile> cmdline -p <process ID>`
7. Als we meer info willen weten over een bestand, `volatility -f <file> --profile=<profile> filescan | grep -i "TYPE HERE THE NAME OF THE FILE"`. Dit zal vervolgens een `offset` terug geven, die nodig is om het bestand te kunnen extraheren van het geheugen, doe dat door `volatility -f <file> --profile=<profile> dumpfiles -Q <OFFSET> -D <folder voor output> file file.dat`
# Lime (Linux Memory Extractor) - Methode voor Linux
Een Loadable Kernel Module (LKM) maakt het mogelijk om vluchtig geheugen vast te leggen van Linux en Linux-gebaseerde apparaten, zoals Android. Dit maakt LiME uniek, omdat het de eerste tool is die volledige geheugencaptures mogelijk maakt op Android-apparaten. Daarnaast minimaliseert het de interactie tussen gebruikers- en kernelruimteprocessen tijdens de verwerving, waardoor het geheugencaptures produceert die forensisch gezien betrouwbaarder zijn dan die van andere tools die zijn ontworpen voor het vastleggen van Linux-geheugen.
1. Download de repo: `git clone git@github.com:504ensicsLabs/LiME.git`
2. Ga vervolgens naar `cd LiME/src`
3. Vervolgens in de `src` folder, type `make` - het is belangrijk dat je gcc en make hebt geinstalleerd door `apt-get install gcc make`
4. Vervolgens begint LiME met compileren, en ontstaat er een bestand genaamd `lime-******-generic.ko`. Dit bestand is nieuw aangemaakt, en is noodzakelijk voor het dumpen van RAM geheugen
5. Zorg ervoor dat je in de `src` folder zit, om de volgende commando uit te voeren. Dit is de commando die verantwoordelijk is voor het uitvoeren van een memory dump van de hele RAM geheugen. `sudo insmod ./lime-******-generic.ko "path=../Linux.mem format=raw"`.
	6. `insmod` zorgt ervoor dat het bestand wordt `ge-insert` in jouw memory
	7. `path` is verantwoordelijk voor de output van de memory dump
	8. `format` is de formaat van jouw memory dump, in ons geval `raw`
6. Vervolgens loopt het vast, en is het bezig met dumpen, geef het een beetje tijd. (**Dit is ook afhankelijk van de grote aan RAM**)

# Ram DUMP

Om te kunne beginnen met dit onderdeel, zijn er een aantal packages nodig om te installeren. `sudo apt install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386`
Ook moet het user account van UBUNTU lid zijn van een plugdev groep `usermod -aG plugdev $LOGNAME` en vervolgens `sudo apt install android-sdk-platform-tools-common`. Voor dit onderzoek is de documentatie van (A guide to Android memory forensics) gebruikt tot **BLZ 9** - https://www.pwc.be/en/FY21/documents/Android_memory_forensics.pdf

1. Maak een AVD aan en kies image `armeabi-v7a`. (Kies voor een 32 BIT image, aangezien 64 BIT niet ondersteund wordt door Volatility)
2. De SD-CARD moet hoger zijn en meer MB hebben dan de RAM geheugen (aangezien je het daar gaat DUMPEN)
3. Zorg voor ROOT
4. Ga naar de Root van UBUNTU en type `git clone https://android.googlesource.com /kernel/goldfish`
5. Vervolgens `cd goldfish` -> `git branch -a` -> `git checkout` -> `git checkout remotes/origin/android-goldfish-2.6.29`
6. Nu heb je een compiler nodig. De compiler die hoort bij `armeabi-v7a` is `git clone --depth=1 https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/arm/arm-eabi-4.8`
7. Vervolgens zet je de variables: `export ARCH=arm` en de compiler `export CROSS_COMILE=~/Android/arm-eabi-4.8/bin/arm-eabi-`
8. Clean alle voorgaande files, voor de zekerheid `cd ~/goldfish` en dan `make clean`
9. Nu moeten we een config file genereren van het apparaat. Type `adb pull /proc/config.gz` (zorg dat je toestel de heletijd aanstaat in dit proces) -> `gunzip config.gz` -> vervolgens `mv config ~/goldfish/.config`
10. Vervolgens lopen we de config na door `make menuconfig`
11. Daarna zorg je dat `Enable loadable module support` en `Module unloading` aan staan. Om `Module unloading` klik je op `Enable loadable module support`. Daarna Save je en exit je
12. Vervolgens `make modules_prepare`
13. En daarna `make`
14. Vervolgens zal de compilatie starten, en zal er een `zImage` gemaakt worden in `arch/arm/boot`

### In onze situatie is er gekozen voor compiler `prebuilts/gcc/linux-x86/x86/i686-linux-android-4.7`, aangezien onze image een `x86` image is.

Onze ARCH is `export ARCH=x86` en onze cross_compile is `export CROSS_COMILE=~/Android/i686-linux-android-4.7/bin/i686-linux-android-`

Bij het downloaden van `i686-linux-android-4.7` is er een probleem, wat onduidelijk is.

1. Clone de folder naar je desktop `git clone https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/x86/i686-linux-android-4.7`
2. Vervolgens zal je zien dat de folder leeg is. Dit komt omdat er op `03-11-2021` een commit heeft plaatsgevonden, waarbij alles is verwijderd!!!
3. Er is vervolgens gekeken naar de commit history, en er is gekozen om een checkout te doen naar `git checkout 8420ac1de63c753d4c5f391a18af476e91a9ac06 -- .`
4. Nu zijn alle bestanden terug en kunnen we verder met het uitvoeren van de compilatie

## !!!!!!! HIER IS HET ONDERZOEK GESTOPT, WE ZIJN WEL WAT VERDER GEKOMEN !!!!!!!!!

# <a name="_toc1582342559515"></a>**Volatility**

1. Download de laatste versie: `git clone https://github.com/volatilityfoundation/volatility3.git`
2. Het is handig om dit te plaatsen in de root `~/`
3. Controleer of alles goed werkt -> `cd volatility3`
4. Vervolgens `python3 vol.py -h`

# <a name="_toc158559515"></a>**APK INSTALL ADB**

Om een APK-bestand te installeren, zonder Play Store. Voer het volgende uit:

1. Download een APK naar keuze, zie apk-mirror
1. Controleer of het apparaat aan staat door `adb devices`
1. Vervolgens type je `adb install <APK FILE NAME>`
1. Controleer door `adb shell pm list packages` of zie of de app op de telefoon verschenen is.

Belangrijke opmerking: bij `adb install`, controleer of **.apk** aanwezig is, anders werkt het niet

# <a name="_toc158559516"></a>**Frida (Tool)**

Het is belangrijk dat je de volgende packages installeert met `PIP` of `PIP3`. Het kan voorkomen dat _frida_ niet gevonden kan worden. Herhaal dan de pip installatie met _sudo_ rechten.

1. Installeer de volgende packages:
   1. `pip install frida-tools`
   1. `pip install frida`
1. Bekijk vervolgens de ABI-list in de Device Info van Android Studio. In ons geval is het: **_x86_64_**
1. Installeer vervolgens de frida-server-16.1.\*\* [afhankelijk van de versie op GitHub] en kies dan \*\*-android-**x86_64**.xz. Zie hier een lijst met releases: <https://github.com/frida/frida/releases?ref=hackernoon.com>
1. Unzip het bestand. Je ziet nu een bestand zoals: **“frida-server-16.1.11-android-x86_64”**
1. Voer de volgende commando uit: **`adb push frida-server-16.1.11-android-x86\_64 /data/local/tmp`**. Je verplaatst nu het bestand naar /data/local/tmp op het _Android toestel_.
1. Vervolgens type je `adb shell`
   1. Wees in ROOT recht (zie een #)
   1. Ga naar `cd /data/local/tmp`
   1. Type: `chmod 777 **frida-server-16.1.11-android-x86\_64**`
   1. En type vervolgens `**./frida-server-16.1.11-android-x86\_64**`
1. Vervolgens zie je niets meer, er komen geen log gegevens niets. **Het lijkt alsof het vastloopt en dat is juist de bedoeling**.
1. Om te kunnen controleren of alles werkt. Zorg ervoor dat je emulator opgestart is en controleer het met `adb devices`.
1. Type vervolgens in je terminal (van UBUNTU) **`frida-ps -U`**. Nu zie je allemaal processen.

Het is zelfs mogelijk om live te kunnen meekijken. Start een applicatie naar keuzen. Zoek vervolgens de juiste proces naam op door `frida-ps -U`. Vervolgens type je `frida-trace -U -i open [package name]`. Nu verschijnt er een log in de terminal die allen stappen laat zien. Als jij nu een tekst toevoegt, of iets verwijderd, dan zie je live waar dat gebeurt.

# <a name="_toc158559517"></a>**Fridadump**

Om een dump te kunnen maken, is het belangrijk dat je Frida hebt geïnstalleerd (zie bovenste kop).

1. Download het volgende bestand naar `~/` (<git@github.com:Nightbringer21/fridump.git>)
1. Je ziet nu een aantal bestanden. Het belangrijkste voor dit onderzoek is `fridump.py`
1. Om te kunnen controleren of alles werkt, type `python3 fridump.py --help`
1. Om een dump te kunnen maken van een applicatie, type **`python3 fridump.py -U -s [package name]`**
1. Ga vervolgens naar `cd dump`. Deze folder is zojuist aangemaakt, na het dumpen van de MEMORY.
1. Bij het generen van sporen, is het belangrijk dat je met iets unieks komt, wat makkelijk te vinden is voor dit onderzoek. In ons geval was het `rexona`. Om te kunnen testen of dit spoor te vinden is, type **`cat strings.txt | grep -a ‘<jouw zoek term>’ `**

# <a name="_toc158559518"></a>**Vooronderzoek (Rooten Device)**

Het _rooten_ van een device is belangrijk, aangezien vele tools die RAM-dumps creëren afhankelijk zijn van een _rooted devices_.

RootAVD is een tool dat is ontworpen om Android Apparaten te rooten. **Het is belangrijk dat jouw AVD-device aan staat en opgestart is. Ook is het belangrijk dat je kijkt naar jouw ABI-list, in ons geval is dat _x86_64_**.

- Stap 1: Ga naar de root folder in Ubuntu: `~/`
- Stap 2: Type: `git clone [https://gitlab.com/newbit/rootAVD.git`](https://gitlab.com/newbit/rootAVD.git%60)
- Stap 3: Vervolgens type je `cd rootAVD` en daarna run je het bestand `./rootAVD.sh`
- Stap 4: Bekijk onder het kopje `Command Examples` en zoek de eerste regel dat eindigt op `/ramdisk.img`. In ons geval is het: `./rootAVD.sh system-images/android-34/google\_apis\_playstore/x86\_64/ramdisk.img`
- Stap 5: Kopieer de bovenstaande regel in de terminal en druk op enter.
- Stap 6: Na het uitvoeren van het bovenstaande script, zal de emulator automatisch afsluiten. Als dit niet het geval is, dan kan je het zelf uitzetten door de emulator af te sluiten.
- Stap 7: Vervolgens start je de emulator op in `cold boot`.
- Stap 8: Controleer of het apparaat gevonden kan worden met adb en type: `adb devices`. Het resultaat lijkt op: **`emulator-5554 device`**.

Om te kunnen controleren of de emulator nu _rooted_ is, voeren we de volgende stappen uit:

1. Stap 1: Start het apparaat op en type in de terminal `adb shell`. Op dit moment zie je **`emu64xa:/ $`**.
1. Stap 2: Je typt vervolgens `su` in de terminal, en op het toestel wordt er gevraagd om toestemming te geven voor root rechten (zie Bijlage 1). Klik op het toestel op **`GRANT`**.
1. Stap 3: Vervolgens veranderd **`emu64xa:/ $`** in de terminal naar **`emu64xa:/ #`**

Het toestel heeft nu _ROOT_ rechten, en dat is te zien aan de **#**. Als het toestel geen _ROOT_ rechten heeft, dan zie je een **$**.

Bijlage 1
