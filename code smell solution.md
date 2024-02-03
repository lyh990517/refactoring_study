# 소프트웨어 개발에서의 리팩토링 전략

## 중복 코드
### 문제
동일하거나 유사한 코드가 여러 곳에서 반복되어 사용됩니다.

### 해결 방법
중복 코드를 별도의 메서드로 추출합니다.


```kotlin
class ReportGenerator {
    fun generateHeader() {
        // 공통 헤더 생성 로직
    }

    fun generateReportForSales() {
        generateHeader()
        // 판매 보고서 특화 로직
    }

    fun generateReportForMarketing() {
        generateHeader()
        // 마케팅 보고서 특화 로직
    }
}
```
## 장황한 메서드
### 문제
하나의 메서드가 너무 길고 여러 작업을 수행합니다.

### 해결 방법
메서드를 더 작은 메서드로 분리합니다.

```kotlin
class OrderProcessor {
    fun processOrder(order: Order) {
        validateOrder(order)
        saveOrder(order)
        notifyCustomer(order)
    }

    private fun validateOrder(order: Order) {
        // 주문 유효성 검사
    }

    private fun saveOrder(order: Order) {
        // 주문 저장
    }

    private fun notifyCustomer(order: Order) {
        // 고객에게 알림
    }
}
```

## 방대한 클래스
### 문제
한 클래스가 너무 많은 역할을 수행합니다.

### 해결 방법
클래스의 일부 기능을 별도의 클래스로 분리합니다.

```kotlin
class UserAccountManager {
    val userValidator = UserValidator()
    val userDatabase = UserDatabase()

    fun createUser(user: User) {
        if (userValidator.isValidUser(user)) {
            userDatabase.saveUser(user)
        }
    }
}

class UserValidator {
    fun isValidUser(user: User): Boolean {
        // 유저 유효성 검사
    }
}

class UserDatabase {
    fun saveUser(user: User) {
        // 유저 데이터베이스에 저장
    }
}
```
## 과다한 매개변수
### 문제
메서드에 너무 많은 매개변수가 전달됩니다.

### 해결 방법
매개변수를 객체로 그룹화합니다.

```kotlin
data class EmailDetails(val recipient: String, val subject: String, val body: String)

class EmailSender {
    fun sendEmail(details: EmailDetails) {
        // 이메일 전송 로직
    }
}

fun main() {
    val emailDetails = EmailDetails("recipient@example.com", "Subject", "Email body")
    EmailSender().sendEmail(emailDetails)
}

```
## 수정의 산발
### 문제
시스템의 변경이 여러 클래스에 걸쳐 필요할 때 발생합니다. 이는 유지보수를 어렵게 만듭니다.

### 해결 방법
변경이 필요한 부분들을 하나의 클래스로 추출하여 중앙 집중화합니다.

```kotlin
class UserSettings {
    // 사용자 설정에 대한 여러 메서드와 속성이 흩어져 있음
}

class UserProfile {
    // 사용자 프로필 관련 메서드와 속성도 흩어져 있음
}

// 수정이 필요한 부분을 UserManagement로 추출
class UserManagement {
    fun updateSettings(settings: UserSettings) {
        // 설정 업데이트 로직
    }

    fun updateProfile(profile: UserProfile) {
        // 프로필 업데이트 로직
    }
}
```
## 기능의 산재
### 문제
클래스 간에 기능이 분산되어 있어 관련 기능을 찾기 어렵습니다.

### 해결 방법
관련 기능을 한 곳으로 모아서 클래스의 책임을 명확하게 합니다.

```kotlin
class Order {
    // 주문 관련 메서드와 속성
}

class Inventory {
    // 재고 관련 메서드와 속성
}

class OrderManager {
    fun placeOrder(order: Order) {
        // 주문 처리 로직
    }

    fun updateInventory(inventory: Inventory) {
        // 재고 업데이트 로직
    }
}
```
## 잘못된 소속
### 문제
메서드가 자신이 속해야 할 클래스에 있지 않아 클래스의 책임이 명확하지 않습니다.

### 해결 방법
메서드를 올바른 클래스로 이동시켜 책임을 명확하게 합니다.

```kotlin
class User {
    fun displayUserName() {
        // 사용자 이름 표시
    }
}

class UserUI {
    fun updateUserUI() {
        // UI 업데이트 로직
    }
}

// displayUserName 메서드를 UserUI 클래스로 이동
class UserUI {
    fun displayUserName(user: User) {
        // 사용자 이름을 UI에 표시하는 로직
    }

    fun updateUserUI() {
        // UI 업데이트 로직
    }
}
```
## 데이터 뭉치
### 문제
여러 곳에서 동일한 데이터 묶음이 반복적으로 사용됩니다.

### 해결 방법
반복적으로 사용되는 데이터를 객체로 묶어 관리합니다.

```kotlin
// 데이터 뭉치 사용 전
class Order {
    fun processOrder(customerName: String, address: String, city: String, zipCode: String) {
        // 주문 처리 로직
    }
}

// 데이터 뭉치를 객체로 전환
data class CustomerAddress(val name: String, val address: String, val city: String, val zipCode: String)

class Order {
    fun processOrder(address: CustomerAddress) {
        // 주문 처리 로직
    }
}
```
## 강박적 기본 타입 사용
### 문제
기본 데이터 타입(예: int, string)을 과도하게 사용하여 프로그램의 명확성이 떨어집니다.

### 해결 방법
기본 타입 대신 클래스를 사용하여 데이터의 의미를 명확히 합니다.

```kotlin
// 기본 타입 사용 전
class Time {
    fun setAlarm(hour: Int, minute: Int) {
        // 알람 설정 로직
    }
}

// 기본 타입 대신 클래스 사용
data class TimePoint(val hour: Int, val minute: Int)

class Time {
    fun setAlarm(timePoint: TimePoint) {
        // 알람 설정 로직
    }
}
```
## switch 문
### 문제
switch 문을 과도하게 사용하면 코드의 유지보수가 어려워집니다.

### 해결 방법
다형성을 이용해 switch 문을 줄이거나 제거합니다.

```kotlin
// switch 문 사용 전
fun calculateDiscount(customerType: String): Double {
    return when (customerType) {
        "Regular" -> 0.05
        "Premium" -> 0.1
        else -> 0.0
    }
}

// 다형성을 이용한 개선
interface Customer {
    fun calculateDiscount(): Double
}

class RegularCustomer : Customer {
    override fun calculateDiscount() = 0.05
}

class PremiumCustomer : Customer {
    override fun calculateDiscount() = 0.1
}
```
## 평행 상속 계층
### 문제
하나의 클래스가 변경될 때 마다, 평행 상속 계층에 있는 다른 클래스도 변경해야 합니다.

### 해결 방법
중복 코드를 제거하고 클래스 간의 적절한 참조를 만들어 관리합니다.

```kotlin
// 평행 상속 계층 예
open class Employee
class Engineer : Employee()
class Salesperson : Employee()

open class EmployeeTest
class EngineerTest : EmployeeTest()
class SalespersonTest : EmployeeTest()

// 평행 상속 계층 개선
open class Employee
class Engineer : Employee()
class Salesperson : Employee()

class EmployeeTest<T : Employee>(private val employee: T) {
    // 테스트 로직
}
```
## 직무유기 클래스
### 문제
어떤 클래스가 거의 아무 기능도 수행하지 않거나, 시간이 지나면서 기능이 크게 줄어들었습니다.

### 해결 방법
기능이 축소된 클래스를 삭제하거나 다른 클래스와 병합합니다.

```kotlin
class DataProcessor {
    // 여러 데이터 처리 관련 메서드
}

class DataLogger {
    // 기록 관련 기능이 거의 없음
    fun logData(data: String) {
        println("Logging: $data")
    }
}

// DataLogger 클래스를 DataProcessor 클래스에 병합
class DataProcessor {
    // 여러 데이터 처리 관련 메서드

    fun logData(data: String) {
        println("Logging: $data")
    }
}
```
## 막연한 범용 코드
### 문제
코드가 너무 범용적이어서 특정 상황에서의 사용 목적이 불분명합니다.

### 해결 방법
범용성을 줄이고, 메서드 명을 명확하게 변경하여 특정 상황에 맞게 조정합니다.

```kotlin
class Utility {
    fun process(data: Any) {
        // 매우 범용적인 처리
    }
}

// 메서드 명과 기능을 구체적으로 변경
class StringProcessor {
    fun processString(str: String) {
        // 문자열 처리 로직
    }
}
```
## 임시 필드
### 문제
클래스 내에 특정 상황에서만 사용되는 필드가 있어 클래스의 일관성이 떨어집니다.

### 해결 방법
해당 필드와 관련된 동작을 별도의 클래스로 추출합니다.

```kotlin
class Order {
    var customerName: String? = null
    var customerAddress: String? = null
    // 여러 주문 관련 메서드
}

// 고객 정보를 별도의 클래스로 추출
class CustomerInfo(var name: String, var address: String)

class Order {
    var customerInfo: CustomerInfo? = null
    // 여러 주문 관련 메서드
}
```
## 메시지 체인
### 문제
클래스가 다른 클래스의 메서드를 호출하고, 그 결과를 사용하여 또 다른 클래스의 메서드를 호출하는 등 긴 메시지 체인이 형성됩니다.

### 해결 방법
대리 객체 은폐를 통해 메시지 체인을 줄입니다.

```kotlin
// 메시지 체인 사용 전
class Customer {
    var account: Account? = null
}

class Account {
    var billingAddress: Address? = null
}

class Address {
    var street: String? = null
}

fun printCustomerStreet(customer: Customer) {
    println(customer.account?.billingAddress?.street)
}

// 대리 객체 은폐
class Customer {
    var account: Account? = null

    fun getBillingStreet(): String? {
        return account?.getBillingStreet()
    }
}

class Account {
    var billingAddress: Address? = null

    fun getBillingStreet(): String? {
        return billingAddress?.street
    }
}

fun printCustomerStreet(customer: Customer) {
    println(customer.getBillingStreet())
}
```
## 과잉 중개 메서드
### 문제
한 클래스가 단순히 다른 클래스의 메서드를 호출하는 데에만 사용되며, 그 자체로는 별다른 기능을 수행하지 않습니다.

### 해결 방법
중개하는 메서드를 제거하고, 클라이언트가 대상 클래스의 메서드를 직접 호출하도록 합니다. 또는 중개 클래스를 대상 클래스의 하위 클래스로 만들어 상속을 이용합니다.

```kotlin
// 과잉 중개 메서드 사용 전
class Client {
    private val processor = DataProcessor()

    fun processData(data: String) {
        processor.processData(data)
    }
}

class DataProcessor {
    fun processData(data: String) {
        // 데이터 처리 로직
    }
}

// 메서드 내용 직접 삽입
class Client {
    private val processor = DataProcessor()

    fun processData(data: String) {
        // 데이터 처리 로직을 여기에 직접 삽입
    }
}
```
## 지나친 관여
### 문제
한 클래스가 다른 클래스의 세부 구현에 지나치게 관여하고 있습니다.

### 해결 방법
메서드 이동과 필드 이동을 통해 각 클래스가 자신의 책임에만 집중하도록 조정합니다.

```kotlin
class User {
    var name: String = ""
}

class UserManager {
    fun printUserName(user: User) {
        println("User name is ${user.name}")
    }
}

// 메서드 이동
class User {
    var name: String = ""

    fun printName() {
        println("User name is $name")
    }
}
```
## 인터페이스가 다른 대용 클래스
### 문제
두 클래스가 유사한 역할을 하지만 서로 다른 인터페이스를 갖고 있어 코드의 일관성이 떨어집니다.

### 해결 방법
메서드 이름을 변경하거나 인터페이스를 통합하여 일관성을 높입니다.

```kotlin
interface DataProcessor {
    fun process(data: String)
}

class XmlProcessor : DataProcessor {
    override fun process(data: String) {
        // XML 처리 로직
    }
}

class JsonProcessor {
    fun parse(data: String) {
        // JSON 처리 로직
    }
}

// 메서드 이름 변경
class JsonProcessor : DataProcessor {
    override fun process(data: String) {
        // JSON 처리 로직
    }
}
```
## 미흡한 라이브러리 클래스
### 문제
사용 중인 라이브러리나 외부 클래스가 필요한 모든 기능을 제공하지 않습니다.

### 해결 방법
라이브러리 클래스를 확장하거나 새로운 메서드를 추가하여 필요한 기능을 구현합니다.

```kotlin
// 라이브러리 클래스 확장
open class LibraryClass {
    fun existingMethod() {
        // 기존 메서드
    }
}

// 필요한 새 메서드 추가
class ExtendedLibraryClass : LibraryClass() {
    fun newMethod() {
        // 새로운 기능 구현
    }
}
```
## 데이터 클래스
### 문제
클래스가 데이터를 저장하는 역할만 하고, 데이터를 처리하는 로직은 없거나 다른 클래스에 있습니다.

### 해결 방법
데이터 클래스가 자체적으로 데이터를 처리할 수 있도록 메서드를 이동하거나 새로운 메서드를 추가합니다. 또한 데이터를 외부에서 직접 접근하지 못하게 하여 캡슐화를 강화합니다.

```kotlin
// 데이터 클래스 예
data class User(var name: String, var age: Int)

class UserManager {
    fun printUserInfo(user: User) {
        println("Name: ${user.name}, Age: ${user.age}")
    }
}

// 메서드 이동 및 캡슐화 강화
data class User(private var name: String, private var age: Int) {
    fun printInfo() {
        println("Name: $name, Age: $age")
    }
}

class UserManager {
    fun processUser(user: User) {
        user.printInfo()
    }
}
```
## 방치된 상속물
### 문제
상속 구조가 복잡하거나, 상속받은 클래스가 부모 클래스의 기능을 제대로 활용하지 못하고 있습니다.

### 해결 방법
상속을 위임으로 전환하거나, 필요 없는 상속 구조를 병합합니다.

```kotlin
// 상속 사용 예
open class BaseClass {
    fun baseFunction() {
        // 기본 기능
    }
}

class DerivedClass : BaseClass() {
    fun derivedFunction() {
        // 확장 기능
    }
}

// 상속을 위임으로 전환
class BaseClass {
    fun baseFunction() {
        // 기본 기능
    }
}

class DerivedClass(private val base: BaseClass) {
    fun derivedFunction() {
        // 확장 기능
        base.baseFunction()
    }
}
```
## 불필요한 주석
### 문제
코드가 복잡하고 의도가 명확하지 않아 주석으로 설명이 필요합니다.

### 해결 방법
코드를 더 명확하게 만들어 주석의 필요성을 줄입니다. 이는 메서드 추출, 메서드명 변경, 어설션 추가 등을 통해 이루어질 수 있습니다.

```kotlin
// 주석이 필요한 예
fun calculateArea(width: Int, height: Int): Int {
    // 사각형의 면적을 계산합니다.
    return width * height
}

// 메서드 추출 및 명확한 메서드명 사용
fun calculateRectangleArea(rectangle: Rectangle): Int {
    return rectangle.calculateArea()
}

class Rectangle(private val width: Int, private val height: Int) {
    fun calculateArea() = width * height
}
```
