
# ❓¿Cómo integraste WHMCS con WordPress usando WHMpress y además lo personalizaste con código propio?

👉 En este artículo te muestro cómo hice una integración entre **WordPress** y **WHMCS** usando el plugin **WHMpress**, agregando lógica personalizada en PHP para detectar la moneda según la IP del visitante, mantenerla en sesión, permitir que el usuario la cambie manualmente y mostrar una banderita al lado 👇

---

## ⚙️ ¿Qué es WHMpress?

[WHMpress](https://codecanyon.net/item/whmpress-whmcs-wordpress-integration-plugin/9946066) es un plugin premium que permite conectar **WordPress** con **WHMCS**, ideal si vendés servicios como hosting, dominios o VPS.

Con WHMpress podés mostrar productos y precios de WHMCS directamente en WordPress, usando shortcodes o widgets.

---

## 🧠 ¿Cuál era la necesidad?

Una empresa de IT necesitaba que:

🌍 Los precios se muestren en la moneda del país del visitante (por IP).  
🧑‍💻 Si el usuario cambia la moneda manualmente, se respete su elección.  
🏁 Agregar una banderita (o ícono) al lado del precio, según la moneda.

---

## 🧩 Solución técnica

A continuación te comparto los **shortcodes** y funciones personalizadas que agregué al archivo `functions.php` del theme:

---

### 🔍 1. Mostrar la IP del visitante

```php
function shortcode_ip_cliente() {
    $ip = $_SERVER['HTTP_CF_CONNECTING_IP'] ?? 
          $_SERVER['HTTP_X_FORWARDED_FOR'] ?? 
          $_SERVER['REMOTE_ADDR'] ?? 'IP no detectada';
    return esc_html($ip);
}
add_shortcode('ip_cliente', 'shortcode_ip_cliente');
```

👉 Uso: `[ip_cliente]`  
Esto permite debuggear o ver desde qué IP se está accediendo.

---

### 🌎 2. Detectar moneda por IP o por URL

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

---

### 🔄 3. Selector visual de moneda

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

👉 Uso: `[selector_moneda]`

---

### 🏁 4. Mostrar una banderita según la moneda

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

👉 Uso: `[moneda_bandera]`

---

## ✨ Resultado final

✅ Precios dinámicos según país  
✅ Cambios manuales de moneda respetados  
✅ Visualización con íconos o banderas  
✅ Todo usando **shortcodes simples** en WordPress
