# Zasady pisania kodu

### 1) Vseobecne

- Na konci php suboru neukoncujeme tagom ?>
- Pole nepiseme formou ```$a = array();``` ale ```$a = [];```
- Premenne budeme pisat bud camelCase ```$myString = '';``` alebo ```$my_string = '';``` (Musime sa dohodnut !!!)
- Odsadzujeme pole podla kluca
```php
$a = [
  'first'        => 0,
  'second_param' => 1
];
```
- Parametre v poli nepiseme camelCase ale oddelene _

### 2) Triedy
##### 2.1) Vseobecne

- Nazov bude camelCase a zacinat velkym pismenom
- Bude obsahovat komentar co robi

```php
/**
 * [Strucny popis jedna veta]
 *
 * [Podrobnejsi popis do detailov obkecany]
 */
class Invoice {
  ...
}
```

##### 2.2) Parametre

- Nazov bude camelCase zacinat malym pismenom
- Bude obsahovat key [private, public...] podla toho na co je potrebny ak ma byt viditelny z vonka pouzijeme public ak nie tak private
- Bude obsahovat default hodnotu v ktorej uz naznacime o aky typ sa jedna
- Bude obsahovat komentar o aky typ sa jedna [string, int...] a popis naco je potrebny

```php
...
class Invoice {
  /** @var string [popis parametra] **/
  public $type = '';
  ...
}
```

##### 2.3) Funkcie

- Nazov bude camelCase zacinat malym pismenom
- Bude obsahovat key [private, public...] podla toho na co je potrebna ak ma byt viditelna/pouzitelna z vonka pouzijeme public ak nie tak private
- Ak bude obsahovat parametre tak budu obsahovat default hodnotu
- Bude obsahovat komentar co robi

```php
...
class Invoice {
  ...
  /**
   * [Strucny popis jedna veta]
   *
   * [Podrobnejsi popis do detailov obkecany]
   *
   * @param array $options [popis parametra]
   * 
   * @return boolean [popis co vracia]
   */
  public function create($options = []) {
    ...
  }
  ...
}
```

### 3) Controller

Sluzi ako remote medzi vstupom a vystupom. Nemal by obsahovat ziadnu vacsiu logiku.
Mal by rozoznat o aky typ requestu ide a podla toho pracovat s vysledkom, teda ci ide o klasicky request alebo API.
- Ak potrebujeme pouzit helpre tak ich inicializujeme cez parameter ```public $helpers = [];```
- Ak potrebujeme pouzit komponenty tak ich inicializujeme cez parameter ```public $components = [];```
- Modely nacitavame cez funkciu ```$this->loadModel('NAME');```

```php
...
class InvoicesController extends AppController {
  ...
  public function create() {
    $result = $this->Invoice->create($this->data);
  
    if ($this->apiRequest) {
      // Je to API request, vratime JSON
      $this->json(['result' => $result]);
    } else {
      // Klasicky request, presmerujeme
      $this->redirect($this->referer);
    }
  }
  ...
}
```

### 4) Model

Sluzi na spracovanie veskerej logiky na zaklade parametrov. Vracia vysledok, idealne pri chybach ich throwuje.

- Vyuzivame vsetky metody cyklu (beforeSave, afterSave...)

```php
...
class Invoice extends AppModel {
  ...
  const TYPES = [
    'regular',
    'proforma'
  ];
  ...
  public function create($options = []) {
    // Idealne je nastavit si default hodnoty aby nas vstup neprekvapil
    $defaults = [
      'id'   => 0,
      'type' => ''
    ];
   
    $options = array_merge($defaults, $options);
    
    if (!in_array($options['type'], self::TYPES)) {
      // Neplatny typ dokladu
      throw new Exception('Invalid type');
    }
    
    return $this->save($options);
  }
  ...
}
```
