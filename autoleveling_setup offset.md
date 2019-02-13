# Výchozí stav
Marlin verze 1.1 a správně zapojená a nakonfigurovaná sonda (indukční / IR / kapacitní)

# Pojmy
..* __Trigger point__ - pomyslný bod v souřadnici Z, ve kterém dojde k sepnutí sondy a ta pošle firmware signál (trigger)

..* __Sensing distance__ - vzdálenost mezi fyzickým okrajem sondy a podložkou, při které dochází k sepnutí sondy a nalezení Trigger pointu

..* __Z-offset__ - vzdálenost mezi Trigger pointem a požadovanou pozicí trysky v bodě 0 na souřadnici Z. 

# Postup
## Pozice sondy
Sonda musí být obecně instalována na tiskové hlavě tak, aby její fyzický okraj byl ideálně alespoň 1mm nad úrovní trysky. To nelze dodržet u sond, které mají reálně malý sensing distance. Příkladem budíž indukční sonda v kombinaci s hlíníkovou vyhřívanou podložkou. (její 4mm sensing distance se na hliníkové podložce mění na cca 1.2mm)

Další předpoklad dobré funkčnosti levelingu je, aby "trigger point" byl alespoň 0.6mm pod úrovní trysky.  Například IR sonda má typicky 2.5mm sensing distance. Při umístění sondy 1.5mm nad úroveň trysky dostaneme bude trigger point cca 1mm pod úrovní trysky.

## Z - offset
Z offset je hodnota, kterou používá firmware Marlin při homingu osy Z a uvádí o kolik se musí vykompenzovat poloha hlavy v ose Z oproti trigger pointu. 

Pokud je trigger point 1mm pod požadovanou úrovní trysky, je třeba nastavit hodnotu Z-offset jako "-1.0" (záporná hodnota).  Konkrétně G-code: "M851 Z-1.0"

## Hledání Z-offset

Po instalaci sondy a ověření základní funkčnosti (sonda bliká a příkaz M119 vrací korektní hodnoty open/triggered) je třeba ověřit, zda je sonda namontována ve správné výšce a poté najít její Z-offset. Z-offset se najde analogicky jako při ručním levelingu (papír pod nahřátou tryskou)

### Příprava

Neprovádejte homing, připojte se na tiskárnu přes OctoPrint, následně vypněte softwarové endstopy příkazem "M211 S0". Teplotu trysky nastavte na 160 stupňů. Připravte si papír, kterým budete hlídat, zda drhne tryska o podložku

__Pozor: od této chvíle musíte pečlivě hlídat jaký příkaz k pohybu vaší tiskárně zadáte. Firmware už pohyb jakkoli neomezuje, pokud budete hloupí, můžete tiskárnu vážně poškodit. __

### Ověření zda je trigger point sondy pod úrovní trysky

Najeďte s tiskovou hlavou v osách X a Y zhruba nad střed podložky. Následně pomalu sjíždějte v ose Z a hlídejte, zda dříve zablíká LED sondy než dojde k drhnutí trysky o papír. Pokud ano, pokračujte dál. Pokud ne, máte sondu moc vysoko, nebo má vaše sonda malý sensing distance proti podložce. V obou případech nemůžete pokračovat.

Zaznamenejte si hodnotu, kterou vaše tiskárna v momentě sepnutí sondy ukazuje na displeji jako pozici na ose Z. (např. Z: -21.2). Pochopitelně neovlivňujte trigger point sondy papírem, ten má být pouze pod tryskou.

### Nalezení požadované úrovně trysky. (pozice Z0)

nyní pomalu klesejte tiskovou hlavou po desetinách milimetru po ose Z do doby, než začne drhnout papír který máte pod tryskou. Ta musí být stále zahřátá, jinak hrozí, že budete po papíru drhnout vyteklým plastem a nenaleznete správný bod. Nalezení správného bodu je otázkou cviku a zkušeností. Pokud si nejste jisti, je lepší jako bod Z0 určit bod, kdy papír drhne méně, než více. Předejdete případnému poškození tiskového povrchu a následně snadno zkompenzujete. Pro příklad jsme nalezli bod Z0 v pozici Z: -22.1). 

Z-offset je tedy rozdíl mezi trigger pointem a bodem Z0:  -21.2 - (-22.1) = -0.9. Hodnota Z-offset je tedy Z-0.9 (G-code:  M851 Z-0.9). Hodnotu je možné zadat do tiskárny. Nezapomeňte uložit nastavení do EEPROM (příkaz M500).

Dále je vhodné ověřit, zda v bodě Z0 je okraj sondy fyzicky dostatečně vysoko nad podložkou. Pokud by sonda drhla o podložku nebo byla příliš nízko, nebylo by s ní možné tisknout, protože by zasahovala do vytisknutého objektu a brzo by se poškodila. 

# Provozní korekce Z-offset

Pokud při tisku první vrstvy vidíme, že je tryska příliš vysoko, je třeba zvýšit absolutní hodnotu Z-offset. Příklad z hodnoty -0.9 na -1.0. V případě, že je první vrstva rozpláclá naopak absolutní hodnotu snižujeme. Vždy postupujeme o 0.1mm, pro finální korekci můžeme upravit o 0.05mm.
