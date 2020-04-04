# Go 문법



## Theory

### Packages and Imports 

만들어 놓은 함수들을 export하려면 node.js처럼 하지않고, 단순히 함수명을 대문자로 시작하면 된다. 시작을 소문자로하게 되면 private가 됨

```go
// path: ./main.go
package main

import (
	"fmt"

	"github.com/hwanyyy/learngo/something"
)

func main() {
	fmt.Println("hello world")
	something.SayHello()
  //something.sayBye() export가 되지 않음
}

```

```go
// path: ../something/something.go
import "fmt"

func sayBye() {
	fmt.Println("Bye")
}

func SayHello() {
	fmt.Println("Hello")

}

```

<br><br>

### Variables and Constants

변수는 var로 사용할 수 있고, 상수는 const로 사용할 수 있다. 변수나 상수명 뒤에 자료형을 써주어야하는데 안써도 알아서 추론해 준다.

```go
var test = "test"
var test string = "test"
const test2 = "test"
const test string = "test"
```

축약형을 사용하면 자동으로 타입을 지정하고, 첫번째 선언한 것의 의존되므로, 타입은 다시 변경 되지 않음

 함수안에서만 가능하고, 변수에만 적용이 가능

```go
func main() {
	varName := "guri "   // var varName string = "guri"
	constName := "hwany" // const constName string = "hwany"
	fmt.Print(varName, constName)		// guri hwany
}
```

<br><br>

### Functions part One

- 파라미터가 모두 동일한 자료형인 경우 `(num1 int, num2 int) `대신 `(num1, num2 int)` 로 축약 가능

```go
func multiply(num1 int, num2 int) int {
	return num1 * num2
}

func main() {
	fmt.Print(multiply(2, 4))		// 8
}

```

- 여러개의 return이 가능

```go
func lenAndUpper(name string) (int, string) {
	return len(name), strings.ToUpper(name)
}

func main() {
	totalLength, upperName := lenAndUpper("hwany")
	fmt.Println(totalLength, upperName) // 5 HWANY
}
```

- return 값은 여러개인데 <u>하나만 사용</u>하고 싶다면? 

하나만 출력하면 될 줄 알았지만, Go언어는 이상하게 사용하지 않으면 에러를 뱉음..

이럴 때는 **underscore(_)**로 표시해주면 된다. Haskell이나 scala에서는 <u>***don't care variable***</u>이라고도 하는데 컴파일 할 때 지나치므로 비슷한 의미로 생각된다.

```go
func lenAndUpper(name string) (int, string) {
	return len(name), strings.ToUpper(name)
}

func main() {
	totalLength, upperName := lenAndUpper("hwany")
	fmt.Println(totalLength)
  /*
  # command-line-arguments
	./main.go:13:15: upperName declared but not used
  */
}
```

**underscore(_)** 를 사용한 경우

```go
func lenAndUpper(name string) (int, string) {
	return len(name), strings.ToUpper(name)
}

func main() {
	totalLength, upperName := lenAndUpper("hwany")
	fmt.Println(totalLength)	// 5
}
```

- 파라미터를 알아서 여러개 받고 싶은 경우

```go
func repeat(words ...string) {
	fmt.Println(words, reflect.TypeOf(words))		// [a b c d e] []string
}

func main() {
	repeat("a", "b", "c", "d", "e")
}
```

- **"naked"** return

return 할 변수를 굳이 명시하지 않아도 됨

파라미터에서 초기화(:=)를 해주고, 변수값을 업데이트함, 이미 return 할 것들을 정의했으므로 그냥 return만 입력

```go
func lenAndUpper(name string) (length int, uppercase string) {
	length = len(name)
	uppercase = strings.ToUpper(name)
	return
}

func main() {
	totalLength, upperName := lenAndUpper("hwany")
	fmt.Println(totalLength, upperName)		// 5 HWANY
}	
```

- **defer**

함수가 완전히 끝났을 때 추가적으로 무엇인가 동작할 수 있도록 함

스크립트언어처럼 `console.log`를 찍는 대신 사용하면 좋을 듯.. 이미지를 열거나 파일 생성하거나, API 요청하거나 할 때 유용하게 사용 가능할 듯 

```go
func lenAndUpper(name string) (length int, uppercase string) {
	defer fmt.Println("Done-!")
	length = len(name)
	uppercase = strings.ToUpper(name)
	return
}

func main() {
	totalLength, upperName := lenAndUpper("hwany")
	fmt.Println(totalLength, upperName)
  /*
  Done-!
	5 HWANY
  */
}
```

<br><br>

### for, range, ...args

forEach나 for in 이나 이런 것 없이 그냥 for하나면 됨. 

```go
func add(numbers ...int) int {
	total := 0
	for _, number := range numbers {		// _ == index
		total += number
	}
	return total
}

func main() {
	result := add(1, 2, 3, 4, 5)
	fmt.Println(result)		// 15
}
```

```go
func add(numbers ...int) {
	for i := 0; i < len(numbers); i++ {
		fmt.Print(numbers[i])		// 12345
	}
}

func main() {
	add(1, 2, 3, 4, 5)
}
```

<br><br>

### if with a Twist

if 부분만을 위한 변수를 만들 수 있는 점이 특이하다. (**variable expression**)

```go
func canIDrink(age int) bool {
	if koreaAge := age + 2; koreaAge < 20 {
		return false
	}
	return true
}

func main() {
	fmt.Println(canIDrink(18))		// true
}
```

<br><br>

### switch

switch에서도 **variable expression**이 가능, if문이나 switch나 다른 언어들에비해 괄호나 콜론이 필요없어서 좀 더 깔끔하다.

```go
func canIDrink(age int) bool {
	switch koreaAge := age + 2; koreaAge {
	case 10:
		return false
	case 20:
		return true
	}

	return false
}

func main() {
	fmt.Println(canIDrink(18))
}
```

<br><br>

### pointer

포인터는 c랑 별 다를게 없다.

```go
func main() {
	a := 2
	b := &a
	*b = 10
	fmt.Println(&a, b, a, *b)		// 0xc0000b4008 0xc0000b4008 10 10
}
```

<br><br>

### Arrays and Slices

array는 기본적으로 c언어같이 초기화 하지않고, 선언만 할 때 처럼(초기화 할 때 길이 생략 가능) 길이를 명시해준다

그런데 길이를 신경 쓰고 싶지 않은 java의 arraylist나 python같은 다른 스크립트 언어처럼 길이를 명시하지 않을 수도 있다.

 이때 slice라는 data type이 있는데 단순히 길이만 제거하면 된다. 추가 할 때에는 `append()`를 쓰는데 slice를 변경하지 않고, 새로운 값이 추가된 slice를 return 하므로 다시 저장해줘야 한다.

```go
func main() {
	// names := [5]string{"shin", "hwany", "guri"} // array
	names := []string{"shin", "hwany", "guri"} // slice
	names = append(names, "new")

	fmt.Println(names)		// [shin hwany guri new]
}
```

<br><br>

### map

map은 단순히 `var ... map[key]value` 처럼 선언을 하게 되면 map은 reference 타입이므로 nil값을 갖게 된다. 즉 어떤 데이터를 쓸 수가 없는데, 초기화하기 위해서 make()를 이용해 `make(map[key]value)` 처럼 감싸줄 수 있다.

```go
func main() {
	//			      [ key ]value
	person := map[string]string{"name": "hwany", "age": "100"}

	fmt.Println(person)		// map[age:100 name:hwany]
}
```

<br><br>

### struct

역시나 c언처럼 class, object가 없고, method나 constructor들은 전부 struct로 부터 나온다.

```go
type person struct {
	name     string
	age      int
	favFoood []string
}

func main() {
	favFoood := []string{"rice", "ramen"}
	// hwany := person{"hwany", 100, favFoood}
	hwany := person{name: "hwany", age: 100, favFoood: favFoood}
	fmt.Println(hwany)
}
```

<br><br>

### Constructor

계정 struct를 만들고, 계정을 생성하는 함수를 만들어서 포인터를 반환하면 복사본이 아닌 개체처럼 사용 가능. 즉, 함수를 사용해서 생성자를 만들 수 있다.

```go
package accounts

// Account struct
type Account struct {
	owner   string
	balance int
}

// NewAccount creates Account
func NewAccount(owner string) *Account {
	account := Account{owner: owner, balance: 0}
	return &account
}
```

```go
func main() {
	account := accounts.NewAccount("hwany")
  fmt.Println(account)		// &{hwany 0}
}

```

<br><br>

### Method

메서드 또한 함수를 사용해 만드는데 아래와 같은 방식으로 만드는데 func과 함수이름 사이에 receiver라는 것을 넣어준다. 

receiver 이름은 설명될 필요도 없기 때문에 한, 두문자로 약어 형태로 쓰는게 좋다고 명시되어 있다.

메서드를 사용할 때 주의해야할 것이 있는데 아래와 같이 Deposit으로 3000을 늘려줘서 결과값을 3000을 기대했지만 0이 나오게 된다. 이유는 main 함수 안에서 Deposit을 호출하면, Deposit receiver에서 복사본`(a Account)`을 만들기 때문

이렇게 관리되는 이유는 method를 보호하고 싶거나 속해있는 개체들이 변경되는걸 원하지 않는 경우도 있기 때문

```go
package accounts

// Account struct
type Account struct {
	owner   string
	balance int
}

// NewAccount creates Account
func NewAccount(owner string) *Account {
	account := Account{owner: owner, balance: 0}
	return &account
}

// Deposit x amount on your account
//  receiver의 이름은 struct 이름의 첫글자를 따서 소문자로 지어야함
func (a Account) Deposit(amount int) {
	a.balance += amount
}

// Balance of your account
func (a Account) Balance() int {
	return a.balance
}
```

```go
func main() {
	account := accounts.NewAccount("hwany")
	account.Deposit(3000)
	fmt.Println(account.Balance())		// 0
}
```

**Deposit method를 호출한 account를 사용하고 싶은 경우** receiver에 **'*'**를 붙이면 된다. *pointer receiver*라고 한다.

*pointer receiver* 는 value와 pointer 모두 접근 가능한데 여기서는 포인터를 리턴하는 생성자를 통해 개체를 만들었으므로 pointer로 접근한 것이다.

 값 변경이 필요없거나 복사본이건 아니건 상관 없는 Balance method는 나름 보안을 위한 것이니 그냥 그대로 두기로..

```go
// Deposit x amount on your account
//  receiver의 이름은 struct 이름의 첫글자를 따서 소문자로 지어야함
func (a *Account) Deposit(amount int) {
	a.balance += amount
}
```

언제 *pointer receiver* 할 지 잘 모르겠다면 아래 가이드를 참고.

- 리시버가 맵, 함수 또는 채널인 경우 포인터를 사용하지 마세요
- 리시버가 슬라이스고 메소드가 리슬라이스나 재할당을 하지 않는 경우 포인터를 사용하지 마세요
- 메소드가 리시버를 변경할 필요가 있다면 포인터를 사용하세요
- 리시버가 구조체고 sync.Mutex 같은 동기화 필드를 가지고 있다면 복사를 피하기 위해 포인터를 사용해야 합니다.
- 리시버가 사이즈가 큰 구조체나 배열이라면 포인터를 사용하는게 효율적입니다.
- 함수나 메소드가 동시에 혹은 메소드에서 호출될 때 리시버를 수정할 수 있습니까? 리비서를 값으로 전달하는 방식은 메소드가 호출될 때 리시버가 복사됩니다. 그래서 리시버를 사용하는 다른 곳에도 영향을 주려면 포인터를 사용하십시오.
- 리시버가 구조체, 배열 혹은 슬라이스이거나 포인터를 요소로 가지고 있다면 포인터를 사용하세요.
- 리시버가 작은 배열이나 구조체라면 값 리시버가 낫습니다. value 메소드에 value가 전달이 된다면 힙을 새로 할당하는 대신 스택에 복사본을 사용할 수 있어서 가바지를 줄일 수 있습니다.
- 마지막으로, 무엇을 이용할지 불분명하다면 포인터를 사용하세요.

출처 : [https://medium.com/@laeshiny/go-code-review-comments-%EC%A0%95%EB%A6%AC-47d05fdb49f6](https://medium.com/@laeshiny/go-code-review-comments-정리-47d05fdb49f6)

원문 출처: https://github.com/golang/go/wiki/CodeReviewComments

<br>

- Go가 내부적으로 호출하는 메서드

python에서 인스턴스 자체를 출력할 때 문자열로 형식을 지정해주는 함수로 `__str__` 함수를 사용한다. 

Go 에서도 자동으로 호출해주는 메서드로 String()메서드가 있는데, 위 코드 account개체를 출력하면 자동으로 String()메서드를 실행해 &{hwany 3000} 으로 나온다. 이를 좀 더 개선해서 출력하고 싶으면 수정하면 된다.



```go
package accounts

import (
	"errors"
	"fmt"
)

// Account struct
type Account struct {
	owner   string
	balance int
}

......

// Owner of the account
func (a Account) Owner() string {
	return a.owner
}

func (a Account) String() string {
	return fmt.Sprint(a.Owner(), "'s account \ndeposit: ", a.Balance())
}

```

```go
func main() {
	account := accounts.NewAccount("hwany")
	account.Deposit(3000)

	fmt.Println(account)
  /*
  hwany's account 
	deposit: 3000
  */
}
```



<br><br>

### error

Go에는 try-catch같은게 없어서 직접 핸들링 해줘야한다. 리턴되는 error 값에는 error와 nil이 있는데 nill은 null이나 none같은 것

```go
package accounts

import "errors"

// Account struct
type Account struct {
	owner   string
	balance int
}

var errNoMoney error = errors.New("Can't withdraw you are poor")

......

// Withdraw x amount from your account
func (a *Account) Withdraw(amount int) error {
	if a.balance < amount {
		return errNoMoney
	}
	a.balance -= amount
	return nil
}
```

```go
func main() {
	account := accounts.NewAccount("hwany")
	account.Deposit(3000)
	err := account.Withdraw(5000)
	if err != nil {
		log.Fatalln(err)
	}
	fmt.Println(account.Balance())
  /*
  2020/03/22 23:11:00 Can't withdraw you are poor
	exit status 1
	*/
}
```

<br><br>

### type

타입을 지정하는 `type`에는 struct에 메서드를 추가하는 것처럼 메서드를 추가할 수 있다.

여기서 map 타입을 지정해 여러가지 메서드를 구현할 것이다. search같은 경우는 그냥 key값으로 접근해도 되지만, 여러 에러처리를 위해 따로 구현한다. 아래와 같이 구현해주면 단순히 `d[word]` 로  접근했을 때보다 탄탄하다.

- Search

```go
package mydict

import "errors"

// Dictionary type
type Dictionary map[string]string

var errNotFound = errors.New("Not Found-!")

// Search for a word
func (d Dictionary) Search(word string) (string, error) {
	value, exists := d[word]
	if exists {
		return value, nil
	}
	return "", errNotFound
}
```

```go
func main() {
	dictionary := mydict.Dictionary{"first": "First word"}
	definition, err := dictionary.Search("second")

	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(definition)		// Not Fount-!
	}
}
```

- Add

```go
package mydict

import "errors"

// Dictionary type
type Dictionary map[string]string

var errNotFound = errors.New("Not Found")
var errWordExits = errors.New("That word already exists")

// Search for a word
func (d Dictionary) Search(word string) (string, error) {
	value, exists := d[word]
	if exists {
		return value, nil
	}
	return "", errNotFound
}

// Add a word to the dictionary
func (d Dictionary) Add(word, def string) error {
	_, err := d.Search(word)

	switch err {
	case errNotFound:
		d[word] = def
	case nil:
		return errWordExits
	}
	return nil
}

```

```go
func main() {
	dictionary := mydict.Dictionary{}
	word := "hello"
	definition := "Greeting"

	err := dictionary.Add(word, definition)
	if err != nil {
		fmt.Println(err)
	}
	// 여기서 err는 다시 할당하는데 왜 에러가 나지 않을까?
	// -> 여러개의 변수를 선언할 때 하나 이상의 새로운 변수를 선언하면 기존에 존재하는 변수를 새로 선언하지 않고, 기존 변수가 값만 할당
	hello, err := dictionary.Search(word)
	fmt.Println("found", word, "definition:", hello)

	err2 := dictionary.Add(word, definition)
	if err2 != nil {
		fmt.Println(err2)
	}
  /*
  found hello definition: Greeting
	That word already exists
	*/
}
```

- Delete

```go
package mydict

import "errors"

// Dictionary type
type Dictionary map[string]string

var (
	errNotFound   = errors.New("Not Found")
	errWordExits  = errors.New("That word already exists")
	errCantUpdate = errors.New("Cant update non-exsiting word")
)

// Search for a word
func (d Dictionary) Search(word string) (string, error) {
	value, exists := d[word]
	if exists {
		return value, nil
	}
	return "", errNotFound
}

// Add a word to the dictionary
func (d Dictionary) Add(word, def string) error {
	_, err := d.Search(word)

	switch err {
	case errNotFound:
		d[word] = def
	case nil:
		return errWordExits
	}
	return nil
}

// Update a word
func (d Dictionary) Update(word, def string) error {
	_, err := d.Search(word)
	switch err {
	case nil:
		d[word] = def
	case errNotFound:
		return errCantUpdate
	}
	return nil
}

// Delete a word
func (d Dictionary) Delete(word string) {
	delete(d, word)
}	
```

```go
func main() {
	dictionary := mydict.Dictionary{}
	word := "hello"
	dictionary.Add(word, "first")
	dictionary.Delete(word)
	word, err := dictionary.Search(word)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(word)		// Not Found
	}
}
```



<br><br>

### goroutine

Go 언어에서 동시성(Concurrency) 프로그래밍은 매우 간단하게 할 수 있다. 단순히 `go` 라는 키워드만 앞에 입력하는게 전부다.

여기서 동시성을 병렬로 착각하면 안된다. 여러개의 goroutine을 만들더라도 1개의 Cpu에서 시분할(Councurrency)로 처리하기 때문에 동시성은 만족한다. 아래는 golang블로그에 이 둘을 분리하기 위해 소개된 내용이다.

> In programming, concurrency is the composition of independently executing processes, while parallelism is the simultaneous execution of (possibly related) computations. Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.

결론부터 말하자면 프로세스와 쓰레드는 os의 스케줄링에 따라서 움직이고 생성,삭제,컨텍스트 스위치에서 오버헤드가 크다는 것

그래서 go에서는 OS에서 스케줄링 안하고, go runtime에서 스케줄링을 하기 위해 만든 것이 goroutine이다. 즉, 쓰레드와 프로세스를 OS에게 요청하지않고, goroutine을 돌리면 알아서 필요한 경우 쓰레드를 꺼내서 쓴다.(경량 쓰레드라 부르지만 실제로 OS요청이 없으므로 쓰레드라고 부리기는 좀 애매..)

주의해야 하는 점은 아래와 같이하면 main함수가 goroutine을 생성하고, 끝나기때문에 goroutine도 바로 종료된다.(주석을 해제해야함)

```go
func main() {
	go count("hwany")
  //time.Sleep(time.Second * 5)
}

func count(person string) {
	for i := 0; i < 10; i++ {
		fmt.Println(person, i)
		time.Sleep(time.Second)
	}
}
```

하지만 `time.Sleep()` 보다 괜찮은 방식이 있는데 `sync`패키지의 `WaitGroup`구조체를 활용해서 다른 goroutine을 제어할 수 있다.`Add()` 는 기다릴 goroutine의 갯수를 증가시키는 함수이고, Done()` 는 갯수를 줄이는 함수이다.

주의해야 할 점은  `Add()` 한 갯수와 `Done()` 한 갯수가 안맞으면 에러가 발생한다.

```go
func main() {
	var wg sync.WaitGroup
	wg.Add(1)

	go count("hwany", &wg)

	wg.Wait()
}

func count(person string, wg *sync.WaitGroup) {
	defer wg.Done()

	for i := 0; i < 5; i++ {
		fmt.Println(person, i)
		time.Sleep(time.Second)
	}
}
```

<br><br>

### Channels

goroutine들 사이에서 데이터를 주고 받는데 사용되는데, 이때 Channel을 이용하게 된다. Channel은 `make()` 함수를 통해 미리 생성되어야하고, blocking 연산자(<-)를 통해서 데이터를 주고받는다. 또 받는 쪽에서 준비될 때까지 **Channel에서 대기**하므로 별도로 wg로 lock을 걸어주지 않고, 동시성 프로그래밍의 문제인 **데이터 동기화**하는데 사용된다. blocking 연산자 또한 `sync`패키지 처럼 갯수를 맞춰야한다.

![image-20200329012126050](/Users/hwany/Library/Application Support/typora-user-images/image-20200329012126050.png)

main함수 안에서 time.Sleep을 없애도 되는 이유는 채널로부터 뭔가를 받을 때 main에서 어떤 답이 올 때까지 기다리기 때문이다. 타입은  `chan` 뒤에 지정해 줘야한다. 

```go
func main() {
	c := make(chan string)
	people := [2]string{"hwany", "guri"}
	for _, person := range people {
		go count(person, c)
	}
	for i := 0; i < len(people); i++ {
		fmt.Println("Received this message:", <-c)
    /*
    Received this message: guri is person
		Received this message: hwany is person
    */
	}
}

func count(person string, c chan string) {
	time.Sleep(time.Second * 3)
	c <- person + " is person"
}
```

