
# BigClown SDK #

BigClown SDK je modulární API pro Core Module, které nabízí pokroèilé funkce pro práci s  periferiemi a hardwarovými moduly.

Bylo napsáno od nuly pøesnì na míru BigClown ekosystému.
Pro dosažení co nejmenší spotøeby energie a co nejnižších hardwarových nárokù byla zvolena cesta objektového programovacího modelu v jazyce C.
Ze stejných dùvodù bylo také upuštìno od RTOS a byl vytvoøen vlastní plánovaè.

Pokud jste nìkdy programovali Arduino (nadstavba C nazvaná Wiring), pak urèitì naleznete spoleèné znaky s BigClown SDK.
Z dùvodù úspory energie ale hlavní smyèka neprobíhá neustále dokola, ale využívá se callbackù, což jsou funkce, které se zavolají, když Vám SDK chce nìco øíct.
Nìkdy je to pøijatá rádiová zpráva, nebo výsledek mìøení teploty.
V okamžiku, kdy není tøeba nic vykonávat se procesor na urèitý èas uspí a probudí se v okamžiku, kdy pøijde pøerušení ze senzoru, nebo z èasovaèe.

Všechny moduly SDK si v tomto seriálu popíšeme a vysvìtlíme.

Kde program zaèíná?
V programovacích jazycích máte nìjaký vstupní bod.
Mùže to být funkce `main()`, `loop()`.
V BC SDK v jazyce C se kód zaèíná vykonávat ve funkci `main()`.
Zde se ale provádí spousta systémových nastavení a úkolù.
Abychom práci co nejvíce zpøehlednili a zpøíjemnili, vytvoøili jsme vstupní bod uživatelské aplikace v `application_init()` v souboru `app/application.c`.

Obsah souboru `src/application.c` bez žádného dalšího kódu vypadá tedy takto:

``` C
void application_init(void)
{
}
```

## LEDka ##

Pojïme na klasickou ukázku rozblikání LED diody, která je na Core Module umístìna.
Nejprve budu demonstrovat ménì efektivní zpùsob, který ale vysvìtlí základy práce s GPIO piny.

``` C
void application_init(void)
{
    bc_gpio_init(BC_GPIO_LED);
    bc_gpio_set_mode(BC_GPIO_LED, BC_GPIO_MODE_OUTPUT);
}

void application_task()
{
    bc_gpio_toggle_output(BC_GPIO_LED);
	
	// Následující funkce provádí nìco, jako delay()
    bc_scheduler_plan_current_relative(500); // TODO: bude existovat delay()?
}
```

Pøedchozí ukázka sice funguje, ale v bateriových aplikacích nám aktivní èekání ve funkci delay() nedovolí uspat procesor.
Navíc blokující delay() ani nedovolí aby se bìhem èekání provádìly jiné úkoly (tasky).

Pojïme pøedchozí pøíklad upravit tak, aby využíval maximálnì možnosti SDK.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, true);
    bc_led_set_mode(&led, BC_LED_MODE_BLINK);
}
```

Výsledný kód se smrskl do dvou øádkù a vytvoøení instance `bc_led_t led`.
Knihovna LED za nás øeší veškerou inicializaci a èasování na pozadí.
Na pozadí si vytvoøí také vlastní task, který bìží jen v okamžiku zmìny stavu LED.
Po zbylou dobu je task deaktivní a plánovaè procesor uspí.

Blikání mùžete zrychlit, zpomalit, nebo LED úplnì vypnout.
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
| bc_led_pulse | Jednou blikne LED na dobu urèenou parametrem |
| bc_led_set_pattern | Èíselný parametr se použije jako vzor pro rozblikání LEDky. Procházejí se jednotlivé bity parametru a pokud je bit v log. 1, pak se LED na rozsvítí. Napø. 0xF0F0FFF0 |
| bc_led_set_slot_interval | Urèuje jak rychle se mají jednotlivé bity z pøedchozí funkce rychle posouvat. |

Na závìr tu mám ještì jednu hádanku. Zkuste pøijít na to, co za zprávu v morseovce vyblikává následující pøíklad. Èíslo jsem zámìrnì zapsal v desítkové soustavì, protože v hexadecimální soustavì lze vzor blikání snadno odhalit.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, false);
    bc_led_set_slot_interval(&led, 200);
    bc_led_set_pattern(&led, 2849884800);
}
```
