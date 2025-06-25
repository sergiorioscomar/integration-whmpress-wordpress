🌎 2. Detectar moneda por IP o por URL
```php
add_action('init', 'whmpress_definir_moneda_por_url_o_ip');

function whmpress_definir_moneda_por_url_o_ip() {
    if (!session_id()) session_start();

    $monedas = ['USD' => 1, 'ARS' => 2];

    if (isset($_GET['currency']) && isset($monedas[strtoupper($_GET['currency'])])) {
        $_SESSION['whmpress_currency'] = $monedas[strtoupper($_GET['currency'])];
        setcookie('whmpress_currency', $_SESSION['whmpress_currency'], time() + 86400 * 30, "/");
        return;
    }

    if (!isset($_SESSION['whmpress_currency']) && isset($_COOKIE['whmpress_currency'])) {
        $_SESSION['whmpress_currency'] = $_COOKIE['whmpress_currency'];
        return;
    }

    if (!isset($_SESSION['whmpress_currency'])) {
        $ip = $_SERVER['REMOTE_ADDR'];
        $geo = @json_decode(file_get_contents("http://www.geoplugin.net/json.gp?ip={$ip}"));
        $pais = $geo->geoplugin_countryCode ?? 'US';
        $_SESSION['whmpress_currency'] = ($pais === 'AR') ? $monedas['ARS'] : $monedas['USD'];
        setcookie('whmpress_currency', $_SESSION['whmpress_currency'], time() + 86400 * 30, "/");
    }
}
```