# Lab 2 - Razumijevanje *hash* funkcija, ekstenzija 

U sklopu ove vježbe student će se upoznati sa radom hash funkcija kao i sa hexadecimalnom reprezentacijom/notacijom.

## Vježba 1

Cilj ove vježbe je razumijeti da datoteke imaju jedinstvena zaglavlja na osnovu tipa datoteke. Pokazat ćemo kako datoteke ne trebaju imati ekstenziju za koje se trenutno prikazuju. Veoma bitno je kod računalne forenzike detektirati datateke koje imaju promijenjenu ekstenziju, jer one mogu ukazivati na potencijalno skrivanje informacije.

- Iz direktorija [Download](Download) sačuvajte datoteku Lab2_download_1.zip te je raspakirajte.

- Vaš zadatak je saznati ekstenziju navedenih datoteka korištenjem python programskog jezika.

**HINT**: upotrebljavajte [python-magic](https://pypi.org/project/python-magic/) biblioteku:
```python
>>> import magic
>>> magic.from_file("testdata/test.pdf")
'PDF document, version 1.2'
# recommend using at least the first 2048 bytes, as less can produce incorrect identification
>>> magic.from_buffer(open("testdata/test.pdf", "rb").read(2048))
'PDF document, version 1.2'
```

- Skinite na vaše računalo te instalirajte `Winhex` program:
http://www.winhex.com/winhex/hex-editor.html


## Vježba 2

Cilj ove vježbe je pokazati kako dvije datoteke kreirane u na različitim uređajima imaju iste hash otiske ukoliko je njihov sadržaj identičan. Također ćemo pokazati iako je sadržaj datoteke identičan (razlikuje se po kapitalizaciji) hash otisak će u tom slučaju biti isti.

- U Notepad-u kreirajte *text* dokument
- U dokument upišite vrijednost ``test`` te sačuvajte datoteku pod nazivom `test.txt`
- Kreirajte novi dokument te u njega upišite vrijednost ``Test`` te ga sačuvajte pod nazivom `test1.txt`
- korištenjem `Python` programa izračunajte hash vrijednosti navedenih datoteka
- Trebali biste dobiti ove rezultate:

```
--Test.txt--
MD5: 098f6bcd4621d373cade4e832627b4f6
SHA1: a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
SHA256: 9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08

--Test1.txt--
MD5: 0cbc6611f5540bd0809a388dc95a615b
SHA1: 640ab2bae07bedc4c163f679a746f7ab7fb5d1fa
SHA256: 532eaabd9574880dbf76b9b8cc00832c20a6ec113d682299550d7a6e0f345e25
```

## Vježba 3

a) Kreirajte u **Word** programu datoteku te u nju upišite neki sadržaj. Sačuvajte dokument u ekstenziji `.docx` pod nazivom `test` na računalu (`test.docx`). Nakon toga, napravite kopiju word dokumenta te joj promijenite naziv i ekstenziju tako da ime bude identično originalnom dokumentu, dok joj je ekstenzija jednaka ekstenziji slike `.jpg` (`test.jpg`). Hoće li hash otisak (**MD5 i SHA1**) obaju dokumenata biti isti. 

b) Pretpostavimo da je tvrtka prijavila problem korporativne špijunaže u kojem smatraju da im je ukraden/kopiran tekstualni dokument u `PDF-u` iznimne važnosti. Budući da tvrtka ne želi otkriti sadržaj dokumenta, forenzični istražitelj dobiva u uvid hash otisak dokumenta:

`c15e32d27635f248c1c8b66bb012850e5b342119`

Također, sa računala osumnjičene osobe ste izuzeli niz dokumenata koji bi mogli ukazivati na potencijalni dokaz. Dokumente u datoteci `Dokaz.zip` možete preuzeti iz direktorija [Download](Download). Raspakirajte dokumente i napravite analizu te navedite o kojem se dokumentu radi.


