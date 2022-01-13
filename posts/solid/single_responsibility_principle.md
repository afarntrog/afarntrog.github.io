---
layout: post
title: Single Responsibility Principle
---
# Single Responsibility Principle
> Classes should do one thing and one thing well. (Class should have high cohesion.)
### This allows for much easier reuse later on.

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

    
    def pay(self, payment_type, security_code):
        if payment_type == "debit":
            print("Processing debit payment type")
            print(f"Verifying security_code: {security_code}")
            self.status = "paid"
        elif payment_type == "credit":
            print("Processing credit payment type")
            print(f"Verifying security_code: {security_code}")
            self.status = "paid"
        else:
            raise Exception(f"Unknown payment type: {payment_type}")
```
### Problem:
The order class is doing too many things: It should not be handling the payments

### Solution:
Extract out payment to another class and split it up appropriately.
```python
class PaymentProcessor:

    def __init__(self) -> None:
        pass

    def pay_credit(self, order, security_code):
        print("Processing credit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"

    def pay_debit(self, order, security_code):
        print("Processing debit payment type")
        print(f"Verifying security_code: {security_code}")
        self.status = "paid"
```
Update the Order class
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

```
### Call our code.
```python
order = Order()
order.add_item('keyboard', 1, 50)
order.add_item('ssd', 1, 150)
order.add_item('usb cable', 2, 5)
print(f'Order total price: {order.total_price()}')

processor = PaymentProcessor()
processor.pay_debit(order, '12345')
```

This increased the cohesion by making it that order has one responsibility and PaymentProcessor has one responsibility.
However, this did introduce some coupling. In the sense that `PaymentProcessor` now takes an `Order` only. This will be resolved as we continue to "SOLID" these classes.