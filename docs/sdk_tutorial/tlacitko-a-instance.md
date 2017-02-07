
## Tlačítko ##

Pojďme nyní ovládat LED tlačítkem na Core Module.
Opět by šlo využít přímé čtení GPIO pinu.
Použijeme ale další komponentu SDK bc_button, která za nás vyřeší hned několik problémů.
Například ošetří a odfiltruje zákmity, které vznikají při stisku a uvolnění tlačítka.
Dále můžeme zjistit, zda tlačítko bylo stisknuto krátce, nebo dlouze.

Poprvé se nyní setkáme s callback funkcí, do které nám program skočí, když na tlačítku nastane událost.
Nejprve si funkci vytvoříme.
Název funkce můžeme volit libovolný, ale budeme se držet konvencí SDK.
Parametry funkce je potřeba dodržet i když je nyní nebudeme používat všechny.
Také je třeba vytvořit instanci bc_button_t, inicializovat tlačítko a nastavit naši callback funkci v application_init().

``` C
{% include "git+https://github.com/bigclownlabs/bc-core-module/blob/master/examples/led-on-off/application.c" %}
```

## Instance a objektové programování ##

Pro LED a tlačíko jsme si museli vytvořit instance `bc_led_t` a `bc_button_t`.
Tyto vytvořené struktury potom jako pointer posíláme do jednotlivých funkcí jako jejich první parametr.
V jazyce C tímto způsobem nahrazujeme objektový programovací model.
V callback funkci pro naše tlačítko se nám tento objekt `bc_button_t *self` posílá jako první parametr.
Zatím jej nemusíme využívat, ale v callbacku pro senzory se nám instance objektu bude hodit.
V callbacku také zatím nepoužíváme parametr `void *event_param`.
Aby kompilátor zbytečně při kompilaci nevypisoval varování, stačí do funkce napsat tyto parametry na řádek a před ně zapsat `(void)`.
Tím kompilátor donutíme si myslet, že parametry používáme a nevypíše varování.

Teď se možná zeptáte, k čemu jsou instance dobré?
Vytvářejí nám zapouzdření určitého bloku funkcí.
Pokud například budeme chtít mit na volných pinech Core Module další blikající LED, můžeme si snadno vytvořit více blikajících LED následujícím způsobem.

``` C
// Vytvoříme pole dvou instancí LED
bc_led_t led[2];

void application_init(void)
{
    // Blikající LED na Core module - led[0]

    bc_led_init(&led[0], BC_GPIO_LED, false, false);
    bc_led_set_mode(&led[0], BC_LED_MODE_BLINK);

    // Blikající LED na externím pinu P8 - led[1]
    // nastaveno rychlé blikání BC_LED_MODE_BLINK_FAST

    bc_led_init(&led[1], BC_GPIO_P8, false, false);
    bc_led_set_mode(&led[1], BC_LED_MODE_BLINK_FAST);
}
```
