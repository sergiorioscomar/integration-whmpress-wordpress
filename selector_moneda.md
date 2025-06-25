🔄 3. Selector visual de moneda
```php
function selector_moneda() {
    if (!session_id()) session_start();

    $currency_num = $_COOKIE['whmpress_currency'] ?? $_SESSION['whmpress_currency'] ?? 1;

    if ($currency_num === '1') {
        $currency = 'USD'; $currency2 = 'ARS'; $icon1 = '🌐'; $icon2 = '🇦🇷';
    } else {
        $currency = 'ARS'; $currency2 = 'USD'; $icon1 = '🇦🇷'; $icon2 = '🌐';
    }

    $html = '<select onchange="if(this.value) location=this.value;">';
    $html .= "<option value='?currency={$currency}' selected>{$icon1} {$currency}</option>";
    $html .= "<option value='?currency={$currency2}'>{$icon2} {$currency2}</option>";
    $html .= '</select>';

    return $html;
}
add_shortcode('selector_moneda', 'selector_moneda');
```
👉 Uso: [selector_moneda]