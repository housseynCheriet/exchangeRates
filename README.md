# exchangeRates

# The code

```php
<?php

//$pattern = '/"((?:.|n)*?)"s*[:,}]s*/';
$pattern = '/:"([^"]+)"/';
preg_match_all($pattern, file_get_contents($argv[1])/*file_get_contents("input.txt")*/, $matches);
$z = sizeof($matches[1])/3;
$i=0;

while ($i < $z) {
$bin=$matches[1][$i*3];
$amount=$matches[1][$i*3+1];
$currency=$matches[1][$i*3+2];
    
    $binResults = file_get_contents('https://lookup.binlist.net/' .$bin);    
    if (!$binResults)
        break;
    $r = json_decode($binResults);
    $isEu = isEu($r->country->alpha2);
                                          /* https://api.exchangeratesapi.io/latest: Seems it's now Apilayer and paid */
    $rate = @json_decode(file_get_contents('https://api.exchangerate.host/latest'), true)['rates'][$currency]; 
    
    if ($currency == 'EUR' or $rate == 0) {
        $amntFixed = $amount;
    }
    else if ($currency != 'EUR' or $rate > 0) {
        $amntFixed = $amount / $rate;
    }
         
    //echo $amntFixed * ($isEu ? 0.01 : 0.02);
    echo preg_replace('/(\.\d\d).*/', '$1', $amntFixed * ($isEu ? 0.01 : 0.02)) + 0.01;  //0.46180... should become 0.47
    print "\n";
    $i++;
}


function isEu($c) {
    $result = false;
    switch($c) {
        case 'AT':
        case 'BE':
        case 'BG':
        case 'CY':
        case 'CZ':
        case 'DE':
        case 'DK':
        case 'EE':
        case 'ES':
        case 'FI':
        case 'FR':
        case 'GR':
        case 'HR':
        case 'HU':
        case 'IE':
        case 'IT':
        case 'LT':
        case 'LU':
        case 'LV':
        case 'MT':
        case 'NL':
        case 'PO':
        case 'PT':
        case 'RO':
        case 'SE':
        case 'SI':
        case 'SK':
            $result = true;
             ;
    }
    return $result;
}
```

## Example `input.txt` file

```
{"bin":"45717360","amount":"100.00","currency":"EUR"}
{"bin":"516793","amount":"50.00","currency":"USD"}
{"bin":"45417360","amount":"10000.00","currency":"JPY"}
{"bin":"41417360","amount":"130.00","currency":"USD"}
{"bin":"4745030","amount":"2000.00","currency":"GBP"}

```

## Running the code

```
> php app.php input.txt

```
