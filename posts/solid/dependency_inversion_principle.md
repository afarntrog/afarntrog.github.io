---
layout: post
title: Dependency Inversion
---
# Dependency Inversion
### The D in SOLID
> Abstractions should not depend on details. **Details should depend on abstractions**.

### Back to our example.

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

class SMSAuth:
    authorized = False

    def verify_code(self, code):
        print(f'Verifying code: {code}')
        self.authorized = True

    def is_authorized(self):
        return self.authorized
```

We can now use just **one** interface, like we had originally. 
By taking advantage of composition, we can pass in a `SMSAuth` object
to any class that uses authorization.

```python
class DebitPaymentProcessor(PaymentProcessor):

    def __init__(self, security_code: str, authorizer: SMSAuth):
        self.authorizer = authorizer
        self.security_code = security_code

    def pay(self, order):
        if not self.authorizer.is_authorized():
            raise Exception("Not authorized")
        print("Processing debit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"
```

#### Call our code.

```python
order: Order = Order()
order.add_item('keyboard', 1, 50)
order.add_item('ssd', 1, 150)
order.add_item('usb cable', 2, 5)
print(f'Order total price: {order.total_price()}')

auth = SMSAuth()
auth.verify_code(123456)
processor: PaymentProcessor = DebitPaymentProcessor('12345', auth)
processor.pay(order)
```

# Problem:
authorization code only accepts SMSAuth. We want to allow all kinds of authorization, such as push notifications or human test.
# Solution.
Create abstraction and make the details depend on the abstractions.

```python
class Authorizer(ABC):
    @abstractmethod
    def is_authorized(self):
        pass
```

### Make `SMSAuth` inherit from `Authorizer``

```python
class SMSAuth(Authorizer):
    authorized = False

    def verify_code(self, code):
        print(f'Verifying code: {code}')
        self.authorized = True

    def is_authorized(self):
        return self.authorized
```
now we can pass in `Authorizer` to the `PaymentProcessor`s instead of passing in a concrete auth type such as `SMSAuth`.

```python
class DebitPaymentProcessor(PaymentProcessor):

    def __init__(self, security_code: str, authorizer: Authorizer):
        self.authorizer = authorizer
        self.security_code = security_code

    def pay(self, order):
        if not self.authorizer.is_authorized():
            raise Exception("Not authorized")
        print("Processing debit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"
```

### Now let's add another Auth type
```python
class NotARobot(Authorizer):
    authorized = False

    def not_robot(self, ):
        print(f'Verifying not_robot')
        self.authorized = True

    def is_authorized(self):
        return self.authorized
```
```python
order: Order = Order()
order.add_item('keyboard', 1, 50)
order.add_item('ssd', 1, 150)
order.add_item('usb cable', 2, 5)
print(f'Order total price: {order.total_price()}')

auth = NotARobot()
auth.not_robot()
processor: PaymentProcessor = DebitPaymentProcessor('12345', auth)
processor.pay(order)
```
