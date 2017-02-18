# Koncept

Tady jsme setřídili naše myšlenky, které nás inspirovaly při vývoji systému BigClown, a také ti zde popisujeme jednotlivé prvky našeho řešení tak, aby jsi lépe pochopil náš záměr a mohl si rychleji začít stavět svou domácí automatizaci.

## O co nám jde?

Víme, že jednotkou života není sekunda, ale zážitek.
Smysluplné prožití života by podle nás mělo být cílem každého rozumného člověka.
No a my se snažíme pomáhat dosáhnout tohoto cíle všem těm, kteří nechtějí trávit svůj volný čas sledováním nekonečných seriálů nebo nadáváním na politiky.
**Soustředíme se na ty z vás, kteří si chtějí hrát s elektronikou, programovat a učit se nové technologie.**
A aby ti to dávalo nějaký smysl a zároveň tě to bavilo, tak výsledkem naší spolupráce bude tvá originální a funkční domácí automatizace.
A kdo z ostatních to má? :- 
)

## Naše vyjímečnost

V každé přiručce supermanažera jsme se dočetli, že abychom byli úspěšní, tak musíme být unikátní a nejlépe, aby na celém světě nebyl nikdo stejný...
My jsme se soustředili hlavně na vývoj a výrobu, ale tady je náš mix vlastností, který nás dělá světově unikátním :-)

1. **Jsme open-source**, protože chceš do svého systému vidět a mít volnost ho propojit s čímkoliv co si umaneš
2. **Děláme spolehlivou elektroniku** s 10letou zárukou, protože se nechceš trápit nekvalitou
3. **Na bezpečnost a spotřebu jsme mysleli už při návrhu**, protože nechceš aby ti soused reguloval topení a nechceš měnit baterky každý týden
4. K sestavení naší elektroniky **nepotřebuješ pájku**, protože svůj čas chceš věnovat vymýšlením originálních funkcí 
5. **Ukazujeme inspirativní projekty**, protože není nic lepšího, než když vylepšíš projekt někoho jiného
6. **Jsme BigClown**, protože kdyby někdo měl těch 5 výše uvedených vlastností, tak nás alespoň odliší tento název :-)

## Naše prvky

### Core Module

#### Pro koho je Core modul zajímavý

Následující popis bude zajímavý zejména pro ty z vás, kteří si chtějí psát vlastní firmware.
Pro ostatní je důležité vědět, že **Core Module je nezbytným prvkem všech našich jednotek a že po zacvaknutí do sestavy funguje bez nutnosti cokoliv dělat...**

#### Ideový základ Core modulu

Domácí automatizace stavěná svépomocí musí být modulární se společným jmenovatelem pro všechny prvky - těmi máme na mysli různé senzory, aktory, zobrazovače apod.
Při návrhu jsme věděli o existenci rozhraní Arduino (shield), ale nevyhovovalo nám ani mechanicky, ani rozměrově.
Navíc neumožňuje inteligentní přepínání vstupního zdroje napětí.
My jsme chtěli něco víc kompaktního, něco, co má na sobě ARM procesor, rádio na 868 MHz a standardizovaný konektor pro připojování.
**Tak se zrodila myšlenka Core Module, který je přesně na míru ušitý potřebám modulárního prvku domácí automatizace.**

#### Osazení Core modulu

**Core Module je špičkově vybaven a je schopen zvládnout téměř všechny potřeby kladené na domácí automatizaci.**
Je osazen výkonným ARM mikrokontrolérem Cortex M0+, rádiovým čipem SIPIRIT 1 komunikujícícm v pásmu 868 MHz, najdeš na něm také teplotní senzor, akcelerometr, kryptočip, USBčko, standardní debuggovací konektor, LEDku a tlačítko.
Prostě obrázek mluví za vše... ;-)

#### Firmware Core modulu

Tím jak jsme kolem Core Module stavěli další hardwarové prvky, tak nám bylo jasné, že pouhá HW modularita nestačí a to, co na trhu v nepřeberné změti převážně čínských modulů chybí, je **dobře navržené a unifikované deklarativní API pro efektivní vývoj firmware.**
Protože trendem poslední doby je dělat event-driven single-threaded aplikace (viz Node.js), tak jsme se tímto nechali inspirovat a základní myšlenku jsme přenesli do embedded API pro náš Core Module, které je v "C".
**Věříme tomu, že právě čisté "C" je efektivní prostředí pro spolehlivé bateriové aplikace.**

### Jednotky 

Jednotkou se u nás rozumí sestava modulů, která komunikuje do systému.
Například si můžete poskládat jednotku s funkcí meteostanice, pohybového detektoru, relé, displeje apod.
Až na pár výjimek je nezbytnou součástí každé jednotky tzv. Core Module, který dává jednotce logiku, zabezpečuje komunikaci a jsou v něm bezpečně uloženy bezpečnostní klíče.
Bezdrátové jednotky komunikují v síti typu hvězda do centrální jednotky, kterou nazýváme [Gateway](###Gateway).
Počet bezdrátových jednotek komunikujících na jednu Gateway je omezen na 64.

#### Moduly a Tagy

Každou jednotku si můžeš poskládat z modulů a tagů.

#### Moduly

Moduly jsou prvky jednotek, jejichž komponenty nebylo možné osadit na formát Tagu (viz níže).
Jejich desky plošných spojů jsou vždy osazeny 14pinovými konektory pro vzájemné propojení a uprostřed mají 4 montážní otvory s horizontální roztečí 2.54 mm a vertikální 4.75 mm.

#### Tagy

Tagy jsou prvky jednotek o velikosti 16 x 16 mm a jsou většinou osazeny právě jedním funkčním prvkem, např. sensorem.
K jednotce je připojíš skrze 5pinovou lištu s kolíky.

### Propojování modulů a tagů

Ke vzájemnému propojování modulů a tagů jsme pro tebe připravili systém konektorů.
U modulů jde na jedné straně o dvě 14pinové dutinkové lišty a na druhé straně o dvě 14pinové lišty s kolíčky.
Aby se zamezilo nesprávnému zapojení, tak na pravých lištách je na šesté pozici odspodu zaslepena jedna dutinka a vynechán jeden kolík.
U tagů jde o 5pinovou lištu s kolíky, které se zasunují do zespodu počítaných prvních pěti dutinek pravé lišty 14pinového konektoru, nebo do samostatných 5pinových dutinkových lišt, které můžete najít na většině našich modulů.
U tagů není ošetřeno nesprávné zasunutí, dej si tedy pozor a tagy zasunuj tak, aby kolíky byly vždy blíže středu modulu než zbytek tagu.

### Senzory

Senzor je zařízení, které detekuje a reaguje na nějaký typ vstupu z fyzického prostředí.
Jde například o pohyb, teplo, světlo, vlhkost ad.
V našem systému jsou senzory většinou ve formátu tagu, výjimkou je například CO2 senzor, jehož komponenty se na tag fyzicky nevejdou.






