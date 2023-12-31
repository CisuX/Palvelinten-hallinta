- Oman moduulin teko

Valitsin projektikseni VLC:n ja kustomoidut configuraatiot siihen.
Halusin vähän rääkätä itseäni lisää ennen lomaa, joten käytän Tehtävässä 6 asennettua W10 virtuaalikonetta https://github.com/CisuX/Palvelinten-hallinta/blob/main/H6.md
Top.sls tiedosto on jo valmiina, sinne lisätään nyt vain tavaraa.

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/7a9b3ff2-717b-4aee-a6f6-70cdacd42f62)

Seuraavaksi salt hakemistoon luodaan vlc.sls tiedosto, pitää muistaa oikea syntaxi YAMLille



Jostain syystä tiedostoa ei löydy, koitin siirrellä sitä eri kansioihin ja ajaa sitä niistä 

(Edit 4.12.2023>  Tajusin että pkg.installedhan etsii asennettuja ohjelmia, ei itse asennusohjelmaa, johtukohan tästä ?)

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/f4bee43e-278b-4f17-b071-848b23f8f77f)

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/fb3b3714-b758-4cde-b79c-e86224ed5bf7)


![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/0964e37d-8b4a-498f-b327-ef1f9d6b9a09)


Selvitellään, kokeillaan chocolateyn kautta > 

Asennetaan käyttäen tätä stringiä chocolateyn sivulta 
```
: Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
Skriptit ei käytössä
![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/ce71b6f5-1cef-47e4-ac1e-dce722df671d)

Täytyy antaa oikeuksia, ajetaan seuraava > 


```
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

```

Nyt pitäisi oikeudet riittää

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/4d2fdd4b-08c3-46f6-9c08-4eaade880c29)

Ajetaan uudestaan chocolateyn stringi ja poistetaan chocolatey kansio programdata kansiosta.


![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/1212c542-b107-4a7b-99b8-0788733310e1)

Pitäisi toimia, bootataan powershell ja testataan

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/25b18c2e-9577-4e8a-845d-7ef10b162a2a)

Toimii, kokeillaan chocolatey.installed muotoa 

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/25f8b199-44bf-40aa-b915-8a291154f9ea)



Jes, toimii !

Seuraavaksi muokataan VLC:n asetuksia ja lisätään niistä uusi vlc-asetukset.sls tiedosto.

VLC:n conffit sijaitsevat täällä > 

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/93fbc71e-1300-4883-a686-8248f65d94a3)

Otetaan täältä asetus joka lisätään vlcnasetukset.sls tiedostoon.


Jostain syystä file.managedia ei löydy, selvitellään.

(Edit 5.12.2023, huomasin että siellähän lukee ini.managed, eikä file.managed)

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/dd60b347-ef16-492d-b218-bc0bd0008cd5)

Kokeilin jotain muuta tyyliä

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/1ac8338e-3138-4599-b37d-de0c71227f7f)

Hups, tämä poisti kaikki rivit ja lisäsi vain tekstin Fullscreen=1


- 4.12.2023, jatkan projektia.


Kokelin toista tapaa vaihtaa asetuksia, nyt tuli aivan uusi errori, selvitellään mistä johtuu.


![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/4b8cfb9a-c1a7-4b9d-845b-6777ad3e367b)

Googlettelin ja löysin artikkelin, jossa saman tyyppistä ongelmaa > https://stackoverflow.com/questions/27092833/unicodeencodeerror-charmap-codec-cant-encode-characters


Nähtävästi encoding täytyy lisätä sls tilaan? 
Kokelin vielä poistaa # merkin fullscreenin edestä ja tämä näytti toimivan.

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/3cd66dbf-d175-42ce-80df-d80985efeabd)


Paitsi että, file.replace nähtävästi korvasi koko tiedoston sisällön ja poisti jostain syystä kaikki conffaukset <.<
![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/a8e50bb8-5166-43eb-b4a5-38c542316faa)

Latasin netistä videon ja kokeilin miten se toimii, yllättäen video aukesi fullscreeninä, vaikea näyttää screenshottina, mutta tässä se nyt silti on.

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/868a3cb1-0629-4f15-9e36-a0a29590f1c5)


Kokeillaan vielä vaihtaa .SLS tiloissa olevia asetuksia idempotenteiksi ( onkohan oikea sanamuoto ?), jotta vlc:tä ei joka kerta asenneta uudestaan.
Lisätään vlc:n sls tilaan versio, jotta se tarkistetaan sls tilaa ajettaessa, ja jos se on asennettuna niin mitään ei pitäisi tapahtua.
Versio päivittyi uusimpaan, aikasempi versio oli 2.2.2 > ja nyt se on 3.0.20

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/4572ed67-f834-4b6e-b51f-be39a58407c1)

Poistetaan vielä testi osio top.sls:ästä ja vastauksen tulisi olla Succeeded: 2 ilman muutoksia >


![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/b65c077c-c8cc-42e0-9657-43db6c4e38d3)

Ajetaan tila
```
salt-call --local state.apply
```


Kaikki toimi niinkun pitikin, olen tyytyväinen.

![image](https://github.com/CisuX/Palvelinten-hallinta/assets/59264168/96fc4947-ff84-459a-aa04-e956774e0b06)



Moduulin kanssa meinasin tulla hieman kiire, koska olen viimeiseltä viikolta poissa ja se että valitsin windowsin käyttikseksi ei ollut kovin fiksu veto...
Jos minulla olisi ollut enemmän aikaa, olisin ladannut muitakin ohjelmia ja tehnyt niille valmiita asetus.sls tiloja, mutta tällä kertaa en kerennyt.

Välillä tuntui siltä että mikään ei toimi niinkun pitää, mutta saltin käyttö vaatii kyllä tarkkuutta ja sen on läksyjen myötä oppinut hyvin.

Projekti tuntui hieman suppealta kun kävin katsomassa muiden tekemiä projekteja, mutta olen silti sitä mieltä että pystyin silti käyttämään oppimiani asioita aika hyvin.

Saltin käyttöä tulen kyllä varmasti opettelemaan lisää, koska pidän sitä erittäin hyödyllisenä työkaluna, ja siitä tulee varmasti olemaan myös apua työelämässä.


- References: 

https://github.com/CisuX/Palvelinten-hallinta/blob/main/H6.md (R.Leppänen, luettu 1.6.2023)

https://terokarvinen.com/2018/04/18/control-windows-with-salt/ (T.Karvinen, 2018, luettu 1.6.2023)

https://chocolatey.org/install (Chocolatey asennusohjeet, luettu 1.6.2023)

https://superuser.com/questions/106360/how-to-enable-execution-of-powershell-scripts (Powershell skriptit, luettu 1.6.2023)

https://wiki.videolan.org/Preferences/ (Vlc configration, luettu 1.6.2023)

https://stackoverflow.com/questions/27092833/unicodeencodeerror-charmap-codec-cant-encode-characters ( SstrykerR, 23.11.2014.)


https://pixabay.com/videos/giraffe-head-mammal-wildlife-191520/ (Video minkä latasin)






