# Contents
[APK INSTALL ADB	3](#_toc158559515)

[Frida (Tool)	4](#_toc158559516)

[Fridadump	5](#_toc158559517)

[Vooronderzoek (Rooten Device)	6](#_toc158559518)






# <a name="_toc158559515"></a>**APK INSTALL ADB**
Om een APK-bestand te installeren, zonder Play Store. Voer het volgende uit:

1. Download een APK naar keuze, zie apk-mirror
1. Controleer of het apparaat aan staat door `adb devices`
1. Vervolgens type je `adb install <APK FILE NAME>`
1. Controleer door `adb shell pm list packages` of zie of de app op de telefoon verschenen is.

Belangrijke opmerking: bij `adb install`, controleer of **.apk** aanwezig is, anders werkt het niet


# <a name="_toc158559516"></a>**Frida (Tool)**
Het is belangrijk dat je de volgende packages installeert met `PIP` of `PIP3`. Het kan voorkomen dat *frida* niet gevonden kan worden. Herhaal dan de pip installatie met *sudo* rechten.

1. Installeer de volgende packages:
   1. pip install frida-tools
   1. pip install frida
1. Bekijk vervolgens de ABI-list in de Device Info van Android Studio. In ons geval is het: ***x86\_64***
1. Installeer vervolgens de frida-server-16.1.\*\* [afhankelijk van de versie op GitHub] en kies dan \*\*-android-**x86\_64**.xz. Zie hier een lijst met releases: <https://github.com/frida/frida/releases?ref=hackernoon.com>
1. Unzip het bestand. Je ziet nu een bestand zoals: **“frida-server-16.1.11-android-x86\_64”**
1. Voer de volgende commando uit: **`adb push frida-server-16.1.11-android-x86\_64 /data/local/tmp`**. Je verplaatst nu het bestand naar /data/local/tmp op het *Android toestel*.
1. Vervolgens type je `adb shell`
   1. Wees in ROOT recht (zie een #)
   1. Ga naar “/data/local/tmp”
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
Het *rooten* van een device is belangrijk, aangezien vele tools die RAM-dumps creëren afhankelijk zijn van een *rooted devices*.

RootAVD is een tool dat is ontworpen om Android Apparaten te rooten. **Het is belangrijk dat jouw AVD-device aan staat en opgestart is. Ook is het belangrijk dat je kijkt naar jouw ABI-list, in ons geval is dat *x86\_64***.

- Stap 1: Ga naar de root folder in Ubuntu: `~/`
- Stap 2: Type: `git clone [https://gitlab.com/newbit/rootAVD.git`](https://gitlab.com/newbit/rootAVD.git%60)
- Stap 3: Vervolgens type je `cd rootAVD` en daarna run je het bestand `./rootAVD.sh`
- Stap 4: Bekijk onder het kopje `Command Examples` en zoek de eerste regel dat eindigt op `/ramdisk.img`. In ons geval is het: `./rootAVD.sh system-images/android-34/google\_apis\_playstore/x86\_64/ramdisk.img`
- Stap 5: Kopieer de bovenstaande regel in de terminal en druk op enter.
- Stap 6: Na het uitvoeren van het bovenstaande script, zal de emulator automatisch afsluiten. Als dit niet het geval is, dan kan je het zelf uitzetten door de emulator af te sluiten.
- Stap 7: Vervolgens start je de emulator op in `cold boot`. 
- Stap 8: Controleer of het apparaat gevonden kan worden met adb en type: `adb devices`. Het resultaat lijkt op: **`emulator-5554 device`**.

Om te kunnen controleren of de emulator nu *rooted* is, voeren we de volgende stappen uit:

1. Stap 1: Start het apparaat op en type in de terminal `adb shell`. Op dit moment zie je **`emu64xa:/ $`**.
1. Stap 2: Je typt vervolgens `su` in de terminal, en op het toestel wordt er gevraagd om toestemming te geven voor root rechten (zie Bijlage 1). Klik op het toestel op **`GRANT`**.
1. Stap 3: Vervolgens veranderd **`emu64xa:/ $`** in de terminal naar **`emu64xa:/ #`**

Het toestel heeft nu *ROOT* rechten, en dat is te zien aan de **#**. Als het toestel geen *ROOT* rechten heeft, dan zie je een **$**.




Bijlage 1

