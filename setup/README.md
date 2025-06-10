<h1>
  <span class="headline">CI & Automation Basics</span>
  <span class="subhead">Setup</span>
</h1>

## Setup

For this project we will need to use github Actions. This feature is available for free on **Github.com**.

## Log in and Create a Repo

Go to [Github](https://github.com/) and sign into your account.

Create a new repository named **`ci-and-automation-basics`**.

    - Set the repo to **Public**
    - ✅ Check the box to include a **README.md**
    - You do **not** need a `.gitignore` file

Using the `Quick Setup` option, clone your newly created repo into your new directory with the `git clone` command:

```bash
git clone https://github.com/<your-username>/ci-and-automation-basics.git
```

> Note: In the link above, where it says `<your-username>`, you should see the username from your GitHub account.

Next, `cd` into your new cloned directory, `ci-and-automation-basics`:

```bash
cd ci-and-automation-basics
```

Open the directory in your code editor:

```bash
code .
```

## Create your starter files

1. Create a new file named `main.py`. This file will contain sample functions related to an e-commerce checkout process.

Paste the following code into it:

```python
# main.py

def add_to_cart(cart, item):
    """
    Adds an item to the cart.
    Item should be a dictionary with 'name' and 'price' keys.
    """
    if not isinstance(item, dict) or 'name' not in item or 'price' not in item:
        raise ValueError("Item must be a dict with 'name' and 'price'.")
    cart.append(item)
    return cart

def calculate_cart_total(cart):
    """
    Calculates the total price of all items in the cart.
    """
    return sum(item['price'] for item in cart)

def apply_shipping(cart_total, shipping_method):
    """
    Applies a flat shipping fee based on method.
    - 'standard': 2.0 BHD
    - 'express': 5.0 BHD
    """
    shipping_fees = {
        'standard': 2.0,
        'express': 5.0
    }
    fee = shipping_fees.get(shipping_method)
    if fee is None:
        raise ValueError("Unsupported shipping method.")
    return cart_total + fee

def apply_payment_fee(cart_total, method):
    """
    Adds a fee for specific payment methods.
    - 'credit_card': 1.0 BHD fee
    - 'cash_on_delivery': 0.5 BHD fee
    - 'benefit_pay': no fee
    """
    fees = {
        'credit_card': 1.0,
        'cash_on_delivery': 0.5,
        'benefit_pay': 0.0
    }
    fee = fees.get(method)
    if fee is None:
        raise ValueError("Unsupported payment method.")
    return cart_total + fee
```

2. Create a file `called test_main.py`. This file will contain tests for your e-commerce functions.

Paste the following code into it:

```python
# test_main.py

import pytest
from main import add_to_cart, calculate_cart_total, apply_shipping, apply_payment_fee

def test_add_to_cart_valid_item():
    cart = []
    item = {'name': 'Wireless Mouse', 'price': 10.0}
    updated_cart = add_to_cart(cart, item)
    assert len(updated_cart) == 1
    assert updated_cart[0]['name'] == 'Wireless Mouse'

def test_add_to_cart_invalid_item():
    cart = []
    with pytest.raises(ValueError):
        add_to_cart(cart, {'name': 'No Price'})

def test_calculate_cart_total():
    cart = [{'name': 'Laptop Sleeve', 'price': 15.0}, {'name': 'USB Cable', 'price': 3.5}]
    total = calculate_cart_total(cart)
    assert total == 18.5

def test_apply_shipping_standard():
    total = apply_shipping(50.0, 'standard')
    assert total == 52.0

def test_apply_shipping_invalid_method():
    with pytest.raises(ValueError):
        apply_shipping(50.0, 'overnight')

def test_apply_payment_fee_credit_card():
    total = apply_payment_fee(100.0, 'credit_card')
    assert total == 101.0

def test_apply_payment_fee_benefit_pay():
    total = apply_payment_fee(100.0, 'benefit_pay')
    assert total == 100.0

def test_apply_payment_fee_invalid():
    with pytest.raises(ValueError):
        apply_payment_fee(100.0, 'paypal')
```

## Optional: Run Your Tests Locally First

Before pushing your code to GitHub, you may want to try running your tests on your own computer to make sure they work.

### Create a Virtual Environment

This keeps your Python dependencies isolated from other projects.

```bash
python3 -m venv venv
```

Activate it:

On macOS/Linux:

```bash
source venv/bin/activate
```

On Windows (Command Prompt):

```bash
venv\Scripts\activate
```

> 💡 You’ll know the environment is active when you see (venv) at the beginning of your terminal prompt.

### Install Required Packages

Install pytest using pip:

```bash
pip install pytest
```

### Run Your Tests

In your project directory (the same folder as `test_main.py`), run:

```bash
pytest
```

You should see output showing all test results (✅ passed or ❌ failed).
