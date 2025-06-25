🔍 1. Mostrar la IP del visitante
```php
function shortcode_ip_cliente() {
    $ip = $_SERVER['HTTP_CF_CONNECTING_IP'] ?? 
          $_SERVER['HTTP_X_FORWARDED_FOR'] ?? 
          $_SERVER['REMOTE_ADDR'] ?? 'IP no detectada';
    return esc_html($ip);
}
add_shortcode('ip_cliente', 'shortcode_ip_cliente');
```
👉 Uso: [ip_cliente]
