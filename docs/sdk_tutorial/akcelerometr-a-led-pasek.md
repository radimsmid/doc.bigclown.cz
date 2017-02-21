
# LED pásek a akcelerometr #

V této kapitole se seznámíš s ovládáním digitálního LED pásku. V druhé části předvedu jak spojit LED pásek s hodnotami z akcelerometru.

## Digitální LED pásek

Je na čase pořádně rozblikat LED pásek na power modulu.
Připoj Power Module, LED pásek a napájecí adaptér. Pásek z důvodu velkého příkonu nelze napájet jen z USB.

Nejprve se podíváme na jednoduchý příklad jak pásek oživit. V dalších kapitolách mu budeme již nastavovat vlastní barvy.

Následující kód po startu spustí funkcí `bc_module_power_led_strip_test()`, která zobrazí testovací animaci na pásku.
Další stisk tlačítka `B` na Core Module provede tento test znova.

``` C
	#include <application.h>

    bc_button_t button;

	void button_event_handler(bc_button_t *self, bc_button_event_t event, void *param)
	{
		(void) self;
		(void) param;
		if (event == BC_BUTTON_EVENT_PRESS)
		{
            bc_module_power_led_strip_test();
		}
	}

	void application_init(void)
	{
		bc_button_init(&button, BC_GPIO_BUTTON, BC_GPIO_PULL_DOWN, false);
		bc_button_set_event_handler(&button, button_event_handler, NULL);

        // Inicializuj Power Module
        bc_module_power_init();
        // Inicializuj buffer, délku a typ pásku
        bc_module_power_led_strip_init(&bc_led_strip_rgbw_144);

        // Spusť testovací animaci
        bc_module_power_led_strip_test();
	}

```

## Akcelerometr

Druhá ukázka je spojení předchozího LED pásku a akcelerometru, který je součástí Core Module. Na LED pásku se uprostřed rozsvítí jeden pixel a bude se posouvat v závislosti na náklonu Core Module doprava a doleva.

Opět musíme vytvořit instanci `lis2dh12` pro objekt akcelerometru.
Po inicializaci je třeba nastavit rychlost vzorkování a callback funkci.
Tato funkce bude volána pokaždé, když akcelerometr naměří novou hodnotu.
V této funkci potom voláním `bc_lis2dh12_get_result_g` získáme výsledné hodnoty zrychlení ve všech třech osách.

My použijeme pouze osu X a rozsah mezi +1g a -1g přepočteme na pozici na LED pásku. Pokud hodnota akcelerometru v ose X bude nulová, bude svítit prostřední pixel s indexem 72.

Podmínkou `if(position >= 0 && position < 144)` si musíme ohlídat rozsahy, protože akcelerometr může vracet i hodnoty větší jak 1 g pokud s ním pohybujeme.

```C
	#include <application.h>

    // Instance akcelerometru
    bc_lis2dh12_t lis2dh12;

    // Callback funkce pro zpracování naměřených dat z akcelerometru
    void lis2dh12_event_handler(bc_lis2dh12_t *self, bc_lis2dh12_event_t event, void *event_param)
    {
        (void) event_param;

        if (event == BC_LIS2DH12_EVENT_UPDATE)
        {
            // Struktura pro naplnění naměřenými hodnotami
            bc_lis2dh12_result_g_t result;

            // Načti naměřené hodnoty do struktury result
            if (bc_lis2dh12_get_result_g(self, &result))
            {
                uint32_t i;
                // Vymaž led pásek
                for( i = 0; i < 144; i++)
                {
                    bc_module_power_led_strip_set_pixel_from_rgb(i, 0 ,0 ,0, 0);
                }

                // Přepočti osu X akcelerometru na pozici  pixelu na LED pásku
                int32_t position = (result.x_axis * 72) + 72;

                if(position >= 0 && position < 144)
                {
                    // Nastav bílou barvu na jeden pixel
                    bc_module_power_led_strip_set_pixel_from_rgb(position, 0, 0, 0, 100);
                }

                // Překresli LED pásek
                bc_module_power_led_strip_write();
            }
        }
    }


	void application_init(void)
	{
        bc_module_power_init();
        bc_module_power_led_strip_init(&bc_led_strip_rgbw_144);

        // Inicializace akcelerometru s nastavením sběrnice I2C0 a adresy 0x19
        bc_lis2dh12_init(&lis2dh12, BC_I2C_I2C0, 0x19);
        // Vzorkuj data každých 20ms
        bc_lis2dh12_set_update_interval(&lis2dh12, 20);
        // Registruj funkci
        bc_lis2dh12_set_event_handler(&lis2dh12, lis2dh12_event_handler, NULL);
	}

```
