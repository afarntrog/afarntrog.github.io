# Liskov Substitution Principle
> Objects of a superclass shall be replaceable with objects of its subclasses without breaking the application

# Back to our example.
```python
class Order:
    items = []
    quantities = []
    prices = []
    status = "open"

    def add_item(self, name, quantity, price):
        self.items.append(name)
        self.quantities.append(quantity)
        self.prices.append(price)

    
    def total_price(self):
        total = 0
        for i in range(len(self.prices)):
            total += self.quantities[i] * self.prices[i]
        return total


from abc import ABC, abstractmethod
class PaymentProcessor(ABC):
    
    @abstractmethod
    def pay(self, order, security_code):
        pass

class DebitPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing debit payment type")
        print(f"Verifying security_code: {security_code}")
        order.status = "paid"

class CreditPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing credit payment type")
        print(f"Verifying security_code: {security_code}")
        order.status = "paid"

class BitcoinPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing Bitcoin payment type")
        print(f"Verifying security_code: {security_code}")
        order.status = "paid"
```
# Problem:
### Imagine we want to add a new payment processor called `PayPalPaymentProcessor`.

```python
class PayPalPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing PayPal payment type")
        print(f"Verifying security_code: {security_code}")
        order.status = "paid"
```

As we know, PayPal does not take a `security_code`, rather they take an email address.
So we would have to "verify" an email as opposed to a `security_code`.
Therefore, we would have to pass in an email in the place of the security_code, like below:
`print(f"Verifying email: {security_code}")

# Call our code.
```python
order: Order = Order()
order.add_item('keyboard', 1, 50)
print(f'Order total price: {order.total_price()}')

processor: PaymentProcessor = PayPalPaymentProcessor()
processor.pay(order, 'test@test.com')
```
## Violation:
This violates the Liskov Substition Principle because security_code should be a security_code and **not** and email.
Meaning, the superclass 'security_code` is not replaceable with email without "breaking" the application.

# Solution:
Use an initializer. In the initializer, we can accept the verification type.

```python
class CreditPaymentProcessor(PaymentProcessor):

    def __init__(self, security_code: str):
        self.security_code = security_code

    def pay(self, order):
        print("Processing credit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"


class PayPalPaymentProcessor(PaymentProcessor):
    def __init__(self, email: str) -> None:
        self.email = email
    def pay(self, order):
        print("Processing PayPal payment type")
        print(f"Verifying security_code: {self.email}")
        self.status = "paid"
```

Now pass the verification method to the **initializer** as opposed to the method.

```python
order: Order = Order()
order.add_item('keyboard', 1, 50)
order.add_item('ssd', 1, 150)
order.add_item('usb cable', 2, 5)
print(f'Order total price: {order.total_price()}')

processor: PaymentProcessor = PayPalPaymentProcessor('test@test.com')
processor.pay(order)
processor: PaymentProcessor = CreditPaymentProcessor('12345')
processor.pay(order)
```