# Interface Segregation Principle
> Better to have multiple **specific** interfaces than one general interface.

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
```

#### let's add another method for sms authentication in our PaymentProcessor class.
```python
from abc import ABC, abstractmethod
class PaymentProcessor(ABC):
    
    @abstractmethod
    def pay(self, order, security_code):
        pass

    @abstractmethod
    def auth_sms(self, code):
        pass

class DebitPaymentProcessor(PaymentProcessor):

    def __init__(self, security_code: str):
        self.security_code = security_code
        self.verified = False

    def pay(self, order):
        print("Processing debit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"

    def auth_sms(self, code):
        print(f"Verifying SMS code: {code}")
        self.verified = True
```

However, what do we do when there is no SMS authentication for a payment type?
For example, credit cards payment doesn't have SMS authentication. In such a case
we have to just raise an exception.
```python
class CreditPaymentProcessor(PaymentProcessor):
    
    def __init__(self, security_code: str):
        self.security_code = security_code

    def pay(self, order, security_code):
        print("Processing credit payment type")
        print(f"Verifying security_code: {security_code}")
        order.status = "paid"

    def auth_sms(self, code):
        raise NotImplementedError
```

# Problem:
We have abstract classes that contain methods that are not applicable to **all** implementations (subclasses).

# Solution (Approach 1):
### Create multiple interfaces.
Create an interface just for SMS authentication.

```python
class PaymentProcessorSMS(PaymentProcessor):
    @abstractmethod
    def auth_sms(self, code):
        pass
```

We can now remove the abstract auth_sms method from the original PaymentProcessor interface.
```python
class PaymentProcessor(ABC):
    
    @abstractmethod
    def pay(self, order, security_code):
        pass
```

And now only use the PaymentProcessorSMS when we use SMS authentication.

```python
class CreditPaymentProcessor(PaymentProcessor):

    def __init__(self, security_code: str):
        self.security_code = security_code

    def pay(self, order):
        print("Processing credit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"

class DebitPaymentProcessor(PaymentProcessorSMS):

    def __init__(self, security_code: str):
        self.security_code = security_code
        self.verified = False

    def pay(self, order):
        print("Processing debit payment type")
        print(f"Verifying security_code: {self.security_code}")
        order.status = "paid"

    def auth_sms(self, code):
        print(f"Verifying SMS code: {code}")
        self.verified = True
```

# Solution (Approach 2 - better):
### Use composition
Create a new class that handles authorization

```python
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

### Call our code.

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