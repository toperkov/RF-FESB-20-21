# RF - Lab 1: BitLocker i dekripcija diska

Kako bi zaštitili laptope, vanjske diskove, USB memorije od posljedica krađe, veoma često se upotrebljavaju tehnike enkripcije cijelih memorija. Jedna od tehnika zaštite podataka je korištenje **BitLocker** alata za šifriranje diska koje je dostupan u novijim verzijama Windowsa (Vista, 7, 8.1 i 10) Ultimate, Pro i Enterprise.

**ZADATAK:** Pretpostavite da ste dobili na analizu USB memoriju čiji je sadržaj enkriptiran korištenjem BitLockera. Nakon što ste napravili sigurnosnu kopiju USB memorije, vaš zadatak je saznati lozinku kojom je enkriptiran disk/USB.

**ZADATAK #2:** Nakon što saznate lozinku i dekriptirate disk, student treba izvući datoteku koja pripada njemu.

## Postupak probijanja Bitlocker lozinke

Probijanje lozinke se sastoji od dva dijela: izvlačenje hash sadržaja iz sigurnosne kopije UBS-a koji je zaštićen lozinkom te stvarnog napada. Sačuvajte na računalu sliku USB-a koji se nalazi na [OneDrive-u](https://fesb-my.sharepoint.com/:u:/g/personal/toperkov_fesb_hr/ERP3tpm9FRRIkk82lCHbQpIBGu-9efbxohQv6dZ6g2B2AQ?e=vmHmwi).

### Izvlačenje hash vrijednosti - [John the Ripper](https://www.openwall.com/john/)

Na računalu sačuvajte sigurnosnu kopiju [USB-a](www.fesb.hr) koji je enkriptiran BitLockerom. Nakon toga, skinite verziju alata [John the Ripper](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.7z) te ga raspakirajte. U nastavku izvucite hash korištenjem `bitlocker2john` alata korištenjem uputa u Terminalu/CMDu:

```python
bitlocker2john -i imageEncrypted
Opening file /path/to/imageEncrypted
```
gdje je `imageEncrypted` sigurnosna kopija USB memorije. Trebali bi dobiti nešto slično u nastavku:

```python
Signature found at 0x00010003
Version: 8
Invalid version, looking for a signature with valid version...

Signature found at 0x02110000
Version: 2 (Windows 7 or later)

VMK entry found at 0x021100d2
VMK encrypted with user password found!
VMK encrypted with AES-CCM

VMK entry found at 0x021101b2
VMK encrypted with Recovery key found!
VMK encrypted with AES-CCM

$bitlocker$0$16$a149a1c91be871e9783f51b59fd9db88$1048576$12$b0adb333606cd30103000000$60$c1633c8f7eb721ff42e3c29c3daea6da0189198af15161975f8d00b8933681d93edc7e63f36b917cdb73285f889b9bb37462a40c1f8c7857eddf2f0e
$bitlocker$1$16$a149a1c91be871e9783f51b59fd9db88$1048576$12$b0adb333606cd30103000000$60$c1633c8f7eb721ff42e3c29c3daea6da0189198af15161975f8d00b8933681d93edc7e63f36b917cdb73285f889b9bb37462a40c1f8c7857eddf2f0e
$bitlocker$2$16$2f8c9fbd1ed2c1f4f034824f418f270b$1048576$12$b0adb333606cd30106000000$60$8323c561e4ef83609aa9aa409ec5af460d784ce3f836e06cec26eed1413667c94a2f6d4f93d860575498aa7ccdc43a964f47077239998feb0303105d
$bitlocker$3$16$2f8c9fbd1ed2c1f4f034824f418f270b$1048576$12$b0adb333606cd30106000000$60$8323c561e4ef83609aa9aa409ec5af460d784ce3f836e06cec26eed1413667c94a2f6d4f93d860575498aa7ccdc43a964f47077239998feb0303105d
```

Kao što je prikazano u primjeru, `bitlocker2john` vraća 4 izlazna hasha s različitim prefiksom.

- Ako je uređaj šifriran metodom provjere autentičnosti korisničke lozinke, `bitlocker2john` ispisuje ta dva hasha:
  - `$bitlocker$0$...:` pokreće način brzog napada korisničke lozinke
  - `$bitlocker$1$...:` pokreće način napada korisničke lozinke s MAC provjerom (sporije izvršavanje, bez _false positives_ rezultata)

- U svakom slučaju, `bitlocker2john` ispisuje sljedeća dva hasha:
  - `$bitlocker$2$...:` pokreće način brzog napada lozinke za oporavak
  - `$bitlocker$3$...:` pokreće način napada za oporavak lozinke s MAC provjerom (sporije izvršavanje, bez _false positives_ rezultata)

Kopirajte hash koji počinje sa `$bitlocker$1$...:` te ga spremite u tekstualnu datoteku (npr. `hash.txt`). Navedena datoteka će se upotrebljavati za drugi alat kojeg ćemo opisati u nastavku - Hashcat.

### Probijanje lozinke - [Hashcat](https://hashcat.net/hashcat/)

Skinite verziju alata [Hashcat](https://hashcat.net/files/hashcat-6.1.1.7z) te ga raspakirajte. U nastavku ćemo koristiti naredbu za probijanje BitLocker lozinke korištenjem `Hashcat` alata iz Terminala/CMDa:

```python
hashcat -m 22100 -a 3 hash.txt "xyz?d?d?d?d?d"
```

pri čemu `-m 22100` predstavlja hash mode za BitLocker, `xyz` predstavlja **HINT** kojeg ćete dobiti od profesora, a `?d?d?d?d?d` je niz od 5 brojeva koji hashcat mora pogoditi _bruteforce_ napadom.

## Podizanje slike kopije diska korištenjem [Arsenal Image Mounter](https://arsenalrecon.com/) alata

Na računalo sačuvajte [Arsenal Image Mounter](https://www.softpedia.com/get/CD-DVD-Tools/Virtual-CD-DVD-Rom/Arsenal-Image-Mounter.shtml) s kojom ćemo podigniti sigurnosnu kopiju USB-a u _Read-only modu_. Kada stisnete tipku `Mount Image`, pronađite sigurnosnu kopiju diska, označite `Read only` te `Create "removable" disk device`. Nakon toga bi se trebao pokazati BitLocker prozor upozorenja za unos lozinke. Kada unesete lozinku trebao bi se pojaviti sadržaj USB memorije.

## Izvlačenje datoteke iz sigurnosne kopije diska

Nakon što ste dekriptirali sigurnosnu kopiju, naći ćete niz datoteka čije je ime generirano primjenom kriptografske _hash_ funkcije SHA-256 prikazano u nastavku:

```python
hash("PrezimeIme" + "SALT") = f3f496e59923ea2f120edbe0b603fac4719bb01e250e9534e401af6f1edb0a5e
```
gdje je za `SALT` korištena BitLocker lozinka.
> **NAPOMENA:**  
> Ime studenta formatirano je kao: `<Prezime><Ime>`. Primjetite kako nema razmaka između prezimena i imena, te da nisu korištena dijakritička slova (čćžšđ), te nema posebnih znakova (npr. -).

Vaš zadatak je saznati datoteku koja pripada studentu, dok je njezin sadržaj rješenje ove vježbe.

> **EDIT:**  
> Korištenjem Google Cloud infrastrukture pokušajte ponoviti probijanje lozinke upotrebom [Cloudtopolis](https://github.com/JoelGMSec/Cloudtopolis) alata te usporedite vrijeme potrebno za doznavanje lozinke u odnosnu sa vrijeme na računalu.