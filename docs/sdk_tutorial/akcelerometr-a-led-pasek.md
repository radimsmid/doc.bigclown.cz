
# LED pásek #

## Digitální LED pásek

Je na čase pořádně rozblikat LED pásek na power modulu.
Připoj Power Module, LED pásek a napájecí adaptér. Pásek z důvodu velkého příkonu nelze napájet jen z USB.

Nejprve se podíváme na jednoduchý příklad jak pásek oživit. V dalších kapitolách mu budeme již nastavovat vlastní barvy.

Následující kód po startu spustí funkcí `bc_module_power_led_strip_test()`, která zobrazí testovací animaci na pásku.
Další stisk tlačítka `B` na Core Module provede tento test znova.

``` C
	#include <application.h>

    bc_button_t button;

    // 144 LED, každá 4 barvy RGBW
    uint8_t frameBuffer[144*4];
    // 144 LED, každá 4 barvy, krát 8 bitů na barvu
    uint8_t bitBuffer[144*4*8];

    // Struktura definující typ pásku, délku a framebuffery
    const bc_led_strip_t led =
    {
        .type = BC_LED_STRIP_TYPE_RGBW,
        .count = 144,
        .framebuffer = frameBuffer,
        .dma_buffer = (uint32_t*)bitBuffer
    };

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

        bc_module_power_init();
        bc_module_power_led_strip_init(&led);
        bc_module_power_led_strip_test();
	}

```
