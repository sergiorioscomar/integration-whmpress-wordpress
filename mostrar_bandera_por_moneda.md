🏁 4. Mostrar una banderita según la moneda
```php
function mostrar_bandera_por_moneda() {
    if (session_status() == PHP_SESSION_NONE) session_start();
    $currency = $_SESSION['whmpress_currency'] ?? null;

    if ($currency == '2') {
        return '<img src="https://flagcdn.com/w40/ar.png" alt="Argentina" width="24" height="18">';
    } elseif ($currency == '1') {
        return '<span style="font-size:24px;">🌐</span>';
    }
    return '';
}
add_shortcode('moneda_bandera', 'mostrar_bandera_por_moneda');
```
👉 Uso: [moneda_bandera]