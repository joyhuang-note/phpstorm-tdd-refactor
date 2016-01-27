# 如何使用PhpStorm實現TDD、重構與偵錯? #

- Origin: [點燈坊：如何使用PhpStorm實現TDD、重構與偵錯?](http://oomusou.io/phpstorm/phpstorm-tdd-refactor/)
- Issue: [https://github.com/joyhuang9473/reading-list/issues/85](https://github.com/joyhuang9473/reading-list/issues/85)

## 設定Domain目錄 ##

> 更改test method名稱，以最能描述測試案例的`口語命名`，不用遵循PSR-2。

- PSR-2 PHP Coding Style: http://oomusou.io/php/php-psr2/

### 3A原則 ###

> 依3A原則為骨架，依次將測試補上。

> 先寫測試讓我們會以測試好寫為前提設計，會幫助我們以使用者需求的抽象化角度去思考架構。

> You are not allowed to write any production code unless it is to make a failing unit test pass. (Uncle Bob -The Three Rules of TDD)

## 第二個測試 ##

> 實務上可以在PHPDoc加上`@group`標籤為test method 分類，如誰寫的測試，哪一個class的測試，方便 `vendor/bin/phpunit` 執行時只跑該group。

    $ phpunit --group=CustomerTest

Note: Return type hint

    /**
     * return Movie object
     *
     * @return Movie
     */
    public function getMovie() : Movie
    {
        return $this->movie;
    }

## 重構 ##

> switch 的確比 if...elseif 容易閱讀，所以實務上也常常會將 if...elseif 重構成 switch

## Extract Method ##

> 改成 `switch` 之後，雖然程式碼已經比較容易閱讀了，但是在calculateTotalPrice()內還是顯得很臃腫，因此想使用重構的 Extract Method 將此 `switch` 重構成一個 method。

## Inline ##

before:

    $price = $this->calculatePrice($order);

    $totalPrice += $price;

after:

    $totalPrice += $this->calculatePrice($order);

## Replace Type Code with State/Stratgey ##

> 在重構的技巧中，有一招叫做**Replace Type Code with State/Strategy**，簡單的說，當你的程式會使用`switch`對同一個變數去做判斷時，可以改用物件導向的多型來處理，或者更白話的說，改用設計模式的**State**模式或**Strategy**模式去處理。

> 這樣的好處是會使你的程式符合SOLID的開放封閉原則，將來若新的需求要新增，將不用去改原來程式的switch，只要去新增class即可。

> 開放封閉原則 : 軟體中的類別、函式對於擴展是開放的，對於修改是封閉的。

> **Self Encapsulate Field**簡單的說，就是將field全部改用`setter`的方式寫入，這樣我們就可以在`setter`內將字串抽象化成物件。

![refactor170](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor170.png)

![refactor122](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor122.png)

![refactor149](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor149.png)

![refactor151](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor151.png)

## Replace Constructor with Factory Method ##

![refactor152](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor152.png)

> 當你使用 `new` 去建立物件時，就直接相依了該物件，我們可將 `new` 物件的邏輯封裝在**Simple Factory**模式內，如此我們就只相依**工廠物件**，而不會直將相依於**計費方式**物件。

![refactor157](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor157.png)

![refactor158](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor158.png)

## Replace Conditional with Polymorphism ##

![refactor159](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor159.png)

> 就是要使用物件導向的多型來取代`switch`，達到SOLID的開放封閉原則。

![refactor160](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/refactor160.png)

> 只要**計費方式**物件改變，`App::bind()`會重新與`AbstractMovieType`連結，但對於`App::make()`來說，都是建立`AbstractMovieType`型別的物件，這就是物件導向的**多型**。

##  我們做了哪些重構? ##

1. Extract Method : 將原本很長的函式利用 Extract Method 拆解成數個小小的 method。
2. Move Method : 利用 Move Method 將 method 搬到它適合的 class 內。
3. 使用多型取代 switch : 若程式內有`switch`，考慮使用物件導向多型的State模式或Strategy模式取代，達成SOLID的開放封閉原則。

## 學習資源 ##

- 測試 : [PHPUnit Testing with Laravel](https://laracasts.com/series/phpunit-testing-in-laravel)
- 重構 : [重構 : 改善既有程式的設計 (二版)](http://www.tenlong.com.tw/items/9861547533?item_id=45657)
- 設計模式 : [大話設計模式](http://www.tenlong.com.tw/items/9866761797?item_id=45056)

## 學習方式 ##

![roadmap](http://oomusou.io/images/phpstorm/phpstorm-tdd-refactor/roadmap.svg)

- 直接由設計模式學習物件導向的學習曲線較為陡峭，很吃天份，失敗機率較高。
- 學習測試與重構達成設計模式的學習曲線較為平緩，適合常人，成功機率較高。

## Conclusion ##

- 好程式不是設計出來的，而是重構出來的。
- 重構一定要搭配測試。TDD讓我們以Top Down方式，以需求出發，幫助我們抽象化思考，不用太早就去思考細節，可以更容易設計出符合SOLID的物件導向程式。

## Sample Code ##

[oomusou/Laravel51Refactor_demo](https://github.com/oomusou/Laravel51Refactor_demo)

issue:

1. type hint

在 php 5.6 ，讓 function parameter 使用 type hint 時，出現 namespace 加到 php 既有型態上的情況  
=> VideoRental\int

1) CustomerTest::test_order_1_regular_movice_with_10_days
ErrorException: Argument 2 passed to VideoRental\Order::__construct() must be an instance of VideoRental\int, integer given, called in /Volumes/Macintosh Data/Laravel51Refactor_demo/tests/CustomerTest.php on line 14 and defined