# Open for extension but closed for modification.
Back to our example.
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


class PaymentProcessor:

    def pay_credit(self, order, security_code):
        print("Processing credit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"

    def pay_debit(self, order, security_code):
        print("Processing debit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"
```
## Problem:
How can we add another payment processor? We can handle debit and credit payments.

But how can we handle Bitcoin payments? We would have to introduce an new method called `def pay_bitcoin`...
```python
class PaymentProcessor:
    def pay_bitcoin(self, order, security_code):
        ...
```
### This violates the open closed principle. We are modifying the `PaymentProcessor` class

## Solution
### Create a structure of classes and subclasses.

```python
from abc import ABC, abstractmethod
class PaymentProcessor(ABC):
    
    @abstractmethod
    def pay(self, order, security_code):
        pass

class DebitPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing debit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"

class CreditPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing credit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"
```

Call our code.

```python
order = Order()
order.add_item('keyboard', 1, 50)
order.add_item('ssd', 1, 150)
order.add_item('usb cable', 2, 5)
print(f'Order total price: {order.total_price()}')

processor = DebitPaymentProcessor()
processor.pay_debit(order, '12345')
```
### We can now just as easily add a new payment processor for Bitcoin.
```python
class BitcoinPaymentProcessor(PaymentProcessor):
    def pay(self, order, security_code):
        print("Processing Bitcoin payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"

processor = BitcoinPaymentProcessor()
processor.pay_debit(order, '12345')
```