#include <zephyr.h>
#include <device.h>
#include <drivers/gpio.h>
// Define the LED and Button pins (specific to your hardware)
#define LED_PIN 13
#define BUTTON_PIN 11
// Configure the GPIO device
#define GPIO_PORT DT_LABEL(DT_NODELABEL(gpio0))
void main(void) {
const struct device *gpio_dev;
int ret;
// Bind the GPIO device
gpio_dev = device_get_binding(GPIO_PORT);
if (!gpio_dev) {
printk("Error: Couldn't find GPIO device\n");
return;
}
// Configure LED as output
ret = gpio_pin_configure(gpio_dev, LED_PIN, GPIO_OUTPUT_ACTIVE);
if (ret < 0) {
printk("Error: Couldn't configure LED pin\n");
return;
}
// Configure Button as input with pull-up and interrupt
ret = gpio_pin_configure(gpio_dev, BUTTON_PIN, GPIO_INPUT | GPIO_PULL_UP);
if (ret < 0) {
printk("Error: Couldn't configure Button pin\n");
return;
}
printk("Button and LED setup complete.\n");
// Toggle the LED when the button is pressed
while (1) {
if (!gpio_pin_get(gpio_dev, BUTTON_PIN)) { // Check if button is pressed
gpio_pin_toggle(gpio_dev, LED_PIN); // Toggle the LED state
k_msleep(200); // Debounce delay
}
}
}
