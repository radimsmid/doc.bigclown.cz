
# BigClown Firmware #

BigClown SDK je modulární API pro Core Module, které nabízí pokročilé funkce pro práci s periferiemi a hardwarovými moduly.

Bylo napsáno od nuly přesně na míru BigClown ekosystému.
Pro dosažení co nejmenší spotřeby energie a co nejnižších hardwarových nároků byla zvolena cesta objektového programovacího modelu v jazyce C.
Ze stejných důvodů bylo také upuštěno od RTOS a byl vytvořen vlastní plánovač.

Pokud jste někdy programovali Arduino (nadstavba C nazvaná Wiring), pak určitě naleznete společné znaky s BigClown SDK.
Z důvodů úspory energie však hlavní smyčka neprobíhá neustále dokola, ale využívá se callbacků, což jsou funkce, které se zavolají, když vám SDK chce něco říct.
Může to být např. přijatá rádiová zpráva nebo výsledek měření teploty.
Ve chvíli, kdy není třeba nic vykonávat, se procesor na určitý čas uspí a probudí se až v okamžiku, kdy přijde přerušení ze senzoru nebo z časovače.

Všechny moduly SDK si v tomto seriálu popíšeme a vysvětlíme.

Kde program začíná?
V programovacích jazycích existuje vstupní bod.
Může to být funkce `main()`, `loop()`.
V BC SDK v jazyce C se kód začíná vykonávat ve funkci `main()`.
Zde se ale provádí spousta systémových nastavení a úkolů.
Abychom práci co nejvíce zpřehlednili a zpříjemnili, vytvořili jsme vstupní bod uživatelské aplikace v `application_init()` v souboru `app/application.c`.

Obsah souboru `app/application.c` bez žádného dalšího kódu vypadá tedy takto:

``` C
void application_init(void)
{
}
```

## LEDka ##

Podívejme se na klasickou ukázku rozblikání LED diody, která je na Core Module umístěna.
Nejprve budu demonstrovat méně efektivní způsob, který ale vysvětlí základy práce s GPIO piny.

``` C
void application_init(void)
{
    bc_gpio_init(BC_GPIO_LED);
    bc_gpio_set_mode(BC_GPIO_LED, BC_GPIO_MODE_OUTPUT);
}

void application_task()
{
    bc_gpio_toggle_output(BC_GPIO_LED);

	// Následující funkce provádí něco, jako delay()
    bc_scheduler_plan_current_relative(500); // TODO: bude existovat delay()?
}
```

Předchozí ukázka sice funguje, v bateriových aplikacích nám však aktivní čekání ve funkci delay() nedovolí uspat procesor.
Navíc blokující delay() ani neumožní, aby se během čekání prováděly jiné úkoly (tasky).

Pojďme předchozí příklad upravit tak, aby využíval maximálně možnosti SDK.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, false);
    bc_led_set_mode(&led, BC_LED_MODE_BLINK);
}
```

Výsledný kód se zkrátil do dvou řádků a vytvoření instance `bc_led_t led`.
Knihovna LED za nás řeší veškerou inicializaci a časování na pozadí.
Na pozadí si vytvoří také vlastní task, který běží jen v okamžiku změny stavu LED.
Po zbylou dobu je task deaktivní a plánovač procesor uspí.

Blikání můžete zrychlit, zpomalit nebo LED úplně vypnout.
Povolené parametry pro funkci bc_led_set_mode naleznete v tabulce níže.

| Enumerator | Popis |
| -----------|-------|
| BC_LED_MODE_OFF | LED has steady off state. |
| BC_LED_MODE_ON | LED has steady on state.|
| BC_LED_MODE_BLINK | LED blinks. |
| BC_LED_MODE_BLINK_SLOW | LED blinks slowly. |
| BC_LED_MODE_BLINK_FAST | LED blinks quickly.|
| BC_LED_MODE_FLASH | LED flashes repeatedly. |

Další zajímavé funkce jsou:

| Funkce | Popis |
|  ---   |  ---  |
| bc_led_pulse | Jednou blikne LED na dobu určenou parametrem |
| bc_led_set_pattern | Číselný parametr se použije jako vzor pro rozblikání LEDky. Procházejí se jednotlivé bity parametru a pokud je bit v log. 1, pak se LED rozsvítí. Např. 0xF0F0FFF0 |
| bc_led_set_slot_interval | Určuje, jak rychle se mají jednotlivé bity z předchozí funkce posouvat. |

Na závěr tady mám ještě jednu hádanku. Zkuste přijít na to, co za zprávu v morseovce vyblikává následující příklad. Číslo jsem záměrně zapsal v desítkové soustavě, protože v hexadecimální soustavě lze vzor blikání snadno odhalit.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, false);
    bc_led_set_slot_interval(&led, 200);
    bc_led_set_pattern(&led, 2849884800);
}
```
