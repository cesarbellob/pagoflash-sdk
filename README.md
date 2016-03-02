#Libreria Cliente PHP para el API de [PagoFlash.com](http://pagoflash.com)

Aquí encontrará la información necesaria para integrar y utilizar el API de [PagoFlash](http://pagoflash.com) en su sitio web. Para utilizar nuestro método de pago debes **[crear una cuenta](https://app.pagoflash.com/profile/account_selection)** de negocios en nuestro site y registrar un **punto de venta**, con esto obtendras la **clave pública (key public)** y la **clave privada (key secret)**, necesarios para integrar el servicio en su sitio web. Si necesitas algún tipo de ayuda puedes envíar un correo a **contacto@pagoflash.com** con el asunto **Integración de botón de pago**.

##Requisitos
- PHP 5.3 o superior
- libreria curl

##Instalación

- Descargar el [sdk](https://raw.githubusercontent.com/PagoFlash/pagoflash-sdk/master/pagoflash.api.client.php) de PagoFlash para php
- Incluir el sdk en su script principal

##Pasos para la integración

Para hacer pruebas ingresa en nuestro sitio de pruebas y [regístra una cuenta de negocios](http://app-test.pagoflash.com/user/business.html), luego de llenar y confirmar tus datos, completa los datos de tu perfil, selecciona el tipo de punto de venta de **comercio electónico**, llena los datos necesarios y una vez registrado el punto utiliza los codigos **key_public** y **key_secret** generados por la plataforma en el sdk como se muestra a continuación:

```php
<?php
include_once('pagoflash.api.client.php');
// url de tu sitio donde deberás procesar el pago
$urlCallbacks = "http://www.misitio.com/procesar_pago.php";
// cadena de 32 caracteres generada por la aplicación, Ej. aslkasjlkjl2LKLKjkjdkjkljlk&as87
$key_public = "tu_clave_publica";
// cadena de 20 caracteres generado por la aplicación. Ej. KJHjhKJH644GGr769jjh
$key_secret = "tu_clave_secreta";
// si desea ejecutar en el entorno de producción pasar (false) en el 4to parametro
$api = new apiPagoflash($key_public,$key_secret, $urlCallbacks,true);

$cabeceraDeCompra = array(
    // Código de la orden (Alfanumérico de máximo 45 caracteres).
    "pc_order_number"   => "001", 
    // Monto total de la orden, número decimal sin separadores de miles, 
    // utiliza el punto (.) como separadores de decimales. Máximo dos decimales
    // Ej. 9999.99
    "pc_amount"         => "20000" 
);

$ProductItems = array();
$product_1 = array(
    // Id. de tu porducto. Ej. 1
    'pr_id'    => 1,
    // Nombre.  127 caracteres máximo.
    'pr_name'    => 'Nombre del producto-servicio vendido', 
    // Descripción .  Maximo 230 caracteres.
    'pr_desc'    => 'Descripción del producto-servicio vendido.', 
    // Precio individual del producto. sin separadores de miles, 
    // utiliza el punto (.) como separadores de decimales. Máximo dos decimales
    // Ej. 9999.99
    'pr_price'   => '20000',
    // Cantidad, Entero sin separadores de miles  
    'pr_qty'     => '1', 
    // Dirección de imagen. debe ser una dirección (url) válida para la imagen.
    'pr_img'     => 'http://www.misitio.com/producto/image/imagen.jpg', 
);

array_push($ProductItems, $product_1);

$pagoFlashRequestData = array(
    'cabecera_de_compra'    => $cabeceraDeCompra, 
    'productos_items'       => $ProductItems
);
$response = $api->procesarPago($pagoFlashRequestData, $_SERVER['HTTP_USER_AGENT']);
$pfResponse = json_decode($response);

if($pfResponse->success){
    ?>
    <a href="<?php echo $pfResponse->url_to_buy ?>" target="_blank">Pagar</a>
    <?php
    //header("Location: ".$pfResponse->url_to_buy);
}else{
    //manejo del error.
}
?>
```
    
##Documentacion del sdk

###Parametros

- **$key_public** *requerido*: identificador del punto de venta, se genera al crear un punto de venta en una cuenta tipo empresa de PagoFlash, formato: UOmRvAQ4FodjSfqd6trsvpJPETgT9hxZ 
- **$key_secret** *requerido*: clave privada del punto de venta, se genera al crear un punto de venta en una cuenta tipo empresa de PagoFlash, formato: h0lmI11KlPpsVBCT8EZi
- **$url_process** *requerido*: url del sitio al cual se realizara la llamada de retorno desde PagoFlash cuando se complete una transaccion.
- **$test_mode** *opcional, false por defecto*: parametro booleano que indica si las transacciones se ralizaran en el entorno de pruebas o el real.

###Plataformas con Plugins PagoFlash
Desarrollamos plugins para las principales plataformas de e-commerce existentes, de esta manera el proceso de integración con PagoFlash es mucho mas sencillo.


![OpenCart](http://www.paygatewayonline.com/wp-content/uploads/2014/10/opencart.png "OpenCart") ![Python](http://snag.gy/pyEp4.jpg "Python") ![Magento](http://www.web-design-phuket.com/images/magento.jpg "Magento") ![Prestashop](http://webpay.svea.com/PageFiles/16088/Prestashop_150x75.png "Prestashop")

[<img src="http://mastermarketingla.com/wp-content/uploads/2015/07/woocommerce.png" width="150">][1111]
  [1111]: https://raw.githubusercontent.com/PagoFlash/pagoflash-sdk/master/pagoflash-woocommerce-plugin.zip
  [2222]: http://mastermarketingla.com/wp-content/uploads/2015/07/woocommerce.png (hover text)




###Valores retornados por PagoFlash
Al finalizar la transacción retornamos un parámetro ('tk') con el cual podrán verificar si la transacción fue satisfactoria o no. Para ello existe el método en nuestro API llamado validarTokenDeTransaccion . A continuación definimos su uso.
```php
<?php
include_once('pagoflash.api.client.php');
// url de tu sitio donde deberás procesar el pago
$url_process = "http://www.misitio.com/procesar_pago.php";
// cadena de 32 caracteres generada por la aplicación, Ej. aslkasjlkjl2LKLKjkjdkjkljlk&as87
$key_public = "tu_clave_publica";
// cadena de 20 caracteres generado por la aplicación. Ej. KJHjhKJH644GGr769jjh
$key_secret = "tu_clave_secreta"; 
//el cuarto parametro (true) es para activar el modo de pruebas para desactivar colocar en **false**
$api = new apiPagoflash($key_public,$key_secret, $urlCallbacks,true);

$response = $api->validarTokenDeTransaccion($_GET["tk"], $_SERVER['HTTP_USER_AGENT']);
$responseObj = json_decode($response, true);

switch ($responseObj["cod"])
{
    // Sucede cuando los parámetros para identificar el punto de venta no coinciden 
    // con los almacenados en la plataforma PagoFlash
    case "4" : 
        print "Prametros recibidos no coinciden"; 
        break;
    // Sucede cuando el token enviado para ser verificado no pertenece al punto de 
    // venta.
    case "6" : 
        print "Transaccion no pertenece a su punto de venta";
        break;
    // Sucede cuando la transacción enviada para ser verificada no fue completada 
    // en la plataforma.
    case "5" : 
        print "Esta transaccion no completada";
        break;
    // Sucede cuando la transacción enviada para ser verificada fue completada 
    // de manera satisfactoria.
    case "1" : 
        print "Transaccion valida y procesada satisfactoriamente";
        break;
}

?>
```