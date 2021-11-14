---
description: https://portswigger.net/web-security/logic-flaws
---

# Business logic vulnerabilities

### Lab: Excessive trust in client-side controls

This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price.

We intercepted the request at Add to cart option.

We can see the request as follows

```
POST /cart HTTP/1.1
...

productId=1&redir=PRODUCT&quantity=1&price=133700
```

We can change price to as we want.

```
productId=1&redir=PRODUCT&quantity=1&price=100
```

Then go to the cart, we can see jacket's value is 1.00$. We can change price as we want.

### Lab: High-level logic vulnerability

This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price.

We intercepted the request at Add to cart option.

We can see the request as follows

```
POST /cart HTTP/1.1
...

productId=1&redir=PRODUCT&quantity=1
```

We sent the request to Repeater. We can change quantity value as we want.

It accepts negative, zero and positive decimal values.

We can change the value as -1.

```
productId=1&redir=PRODUCT&quantity=-1
```

Then go to the cart, we can see jacket's value is -1337.00$. Then we'll face this error: Cart total price cannot be less than zero

So, if we combine positive and negative ones, can we buy them like this?

We intercepted the request at Add to cart option of "Conversation Controlling Lemon".

```
productId=4&redir=PRODUCT&quantity=-26
```

The we can see total amount is $99.40. With this situtation, we can buy these products.

### Lab: Low-level logic flaw

This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price.

We intercepted the request at Add to cart option.

We can set maxiumum value of quantity to 99.

The price has exceeded the maximum value permitted for an integer in the back-end programming language (2,147,483,647). As a result, the value has looped back around to the minimum possible value (-2,147,483,647).

If we can combine values of two products, we can make an order with this limit.

We sent below request to repeater.

```
POST /cart HTTP/1.1
...

productId=1&redir=PRODUCT&quantity=1
```

We added high number of it to get catch the proper value.

```
productId=1&redir=PRODUCT&quantity=99
```

We sent to the request to Intruder with 326 null payloads. We can see total value as -$64060.96

After that we need to add some quantities of it to get proper value. We added 47 more. Then the total is -$1221.96. At this situation we can add other products to get proper value.

Dancing In The Dark - $75.52 - 17 pieces.

At the end, total value was $61.88. It is a proper one. We can place order.

| Name                              | Price    | Quantity |
| --------------------------------- | -------- | -------- |
| Lightweight "l33t" Leather Jacket | $1337.00 | 32123    |
| Dancing In The Dark               | $75.52   | 17       |

### Lab: Inconsistent handling of exceptional input

This lab doesn't adequately validate user input. You can exploit a logic flaw in its account registration process to gain access to administrative functionality.

There is /admin interface. Admin interface is only available if logged in as a DontWannaCry user.

`@exploit-accd1fe01eba9513c0a3965d01ec0079.web-security-academy.net`

At the registration phase, very-long-string@YOUR-EMAIL-ID.web-security-academy.net The very-long-string should be at least 200 characters long.

`$ ./pattern_create.rb -l 250 Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2A`

`aa0aa1aa2aa3aa4aa5aa6aa7aa8aa9ab0ab1ab2ab3ab4ab5ab6ab7ab8ab9ac0ac1ac2ac3ac4ac5ac6ac7ac8ac9ad0ad1ad2ad3ad4ad5ad6ad7ad8ad9ae0ae1ae2ae3ae4ae5ae6ae7ae8ae9af0af1af2af3af4af5af6af7af8af9ag0ag1ag2ag3ag4ag5ag6ag7ag8ag9ah0ah1ah2ah3ah4ah5ah6ah7ah8ah9ai0ai1ai2a@exploit-accd1fe01eba9513c0a3965d01ec0079.web-security-academy.net`

then log in the account, we can see email addresses as

`aa0aa1aa2aa3aa4aa5aa6aa7aa8aa9ab0ab1ab2ab3ab4ab5ab6ab7ab8ab9ac0ac1ac2ac3ac4ac5ac6ac7ac8ac9ad0ad1ad2ad3ad4ad5ad6ad7ad8ad9ae0ae1ae2ae3ae4ae5ae6ae7ae8ae9af0af1af2af3af4af5af6af7af8af9ag0ag1ag2ag3ag4ag5ag6ag7ag8ag9ah0ah1ah2ah3ah4ah5ah6ah7ah8ah9ai0ai1ai2a@expl`

There are 255 chars.

`aa0aa1aa2aa3aa4aa5aa6aa7aa8aa9ab0ab1ab2ab3ab4ab5ab6ab7ab8ab9ac0ac1ac2ac3ac4ac5ac6ac7ac8ac9ad0ad1ad2ad3ad4ad5ad6ad7ad8ad9ae0ae1ae2ae3ae4ae5ae6ae7ae8ae9af0af1af2af3af4af5af6af7af8af9ag0ag1ag2ag3ag4ag5ag6ag7ag8ag9ah0ah1ah2ah3ah4ah5ah6ah7ah8a@dontwannacry.com`

We are matching the value as shown to get access admin rights.

`aa0aa1aa2aa3aa4aa5aa6aa7aa8aa9ab0ab1ab2ab3ab4ab5ab6ab7ab8ab9ac0ac1ac2ac3ac4ac5ac6ac7ac8ac9ad0ad1ad2ad3ad4ad5ad6ad7ad8ad9ae0ae1ae2ae3ae4ae5ae6ae7ae8ae9af0af1af2af3af4af5af6af7af8af9ag0ag1ag2ag3ag4ag5ag6ag7ag8ag9ah0ah1ah2ah3ah4ah5ah6ah7ah8a@dontwannacry.com.exploit-accd1fe01eba9513c0a3965d01ec0079.web-security-academy.net`

Then we need to register again with this email. After the login phase, we can see Admin panel tab.

Your email is: aa0aa1aa2aa3aa4aa5aa6aa7aa8aa9ab0ab1ab2ab3ab4ab5ab6ab7ab8ab9ac0ac1ac2ac3ac4ac5ac6ac7ac8ac9ad0ad1ad2ad3ad4ad5ad6ad7ad8ad9ae0ae1ae2ae3ae4ae5ae6ae7ae8ae9af0af1af2af3af4af5af6af7af8af9ag0ag1ag2ag3ag4ag5ag6ag7ag8ag9ah0ah1ah2ah3ah4ah5ah6ah7ah8a@dontwannacry.com

The application server truncated the address associated with your account to 255 characters. As a result, you have been able to register with what appears to be a valid @dontwannacry.com address.

### Lab: Inconsistent security controls

This lab's flawed logic allows arbitrary users to access administrative functionality that should only be available to company employees.

We registered a new account. There is an info at register page. Registered with - `attacker@exploit-aca21ffe1e6dd48bc05d356c016b0026.web-security-academy.net`

If you work for DontWannaCry, please use your @dontwannacry.com email address

At the my account page there is a Email Update function. Changed with -&#x20;

`attacker2@exploit-aca21ffe1e6dd48bc05d356c016b0026.web-security-academy.net`

There is no need verification. So we can change our email as we want. Changed with - `test@dontwannacry.com`

Then we can access Admin panel.

### Lab: Weak isolation on dual-use endpoint

This lab makes a flawed assumption about the user's privilege level based on their input. As a result, you can exploit the logic of its account management features to gain access to arbitrary users' accounts.

There is a change password function at my account page.

The post request parameters are `csrf=PKVrI8YpD118cXeuRIiB5ULV7osgMvFb&username=wiener&current-password=peter&new-password-1=pass&new-password-2=pass`

We played with these variables. When we deleted the current-password variable from request, we could change password.

`csrf=PKVrI8YpD118cXeuRIiB5ULV7osgMvFb&username=wiener&new-password-1=pass&new-password-2=pass`

Then we tried to reset administrator pass.

It did not check username neither.

`csrf=PKVrI8YpD118cXeuRIiB5ULV7osgMvFb&username=administrator&new-password-1=cel1s0&new-password-2=cel1s0`

So we could change administrator password to cel1s0.

### Lab: Insufficient workflow validation

This lab makes flawed assumptions about the sequence of events in the purchasing workflow.

We bought a product which was “Cheshire Cat Grin”.

We can see workflow as follows:

* POST /cart
* GET /cart
* POST /cart/checkout
* GET /cart/order-confirmation?order-confirmed=true

We can bypass POST /cart/checkout step. So we can avoid validation of having enough money.

We added the product to the cart. Then you need to make this request.&#x20;

`GET /cart/order-confirmation?order-confirmed=true`

### Lab: Authentication bypass via flawed state machine

This lab makes flawed assumptions about the sequence of events in the login process.

We can see workflow as follows:

* GET /login
* POST /login
* GET /role-selector
* POST /role-selector
* GET /

If we can bypass the role-selector part, we can get admin rights.

With the content discovery tool, we can identify /admin path.

Answer: This workflow:

* GET /login
* POST /login
* GET /admin

### Lab: Flawed enforcement of business rules

This lab has a logic flaw in its purchasing workflow.

New customers use code at checkout: NEWCUST5

Sign up to our newsletter! -> Use coupon SIGNUP30 at checkout!

Try applying the codes more than once. Notice that if you enter the same code twice in a row, it is rejected because the coupon has already been applied. However, if you alternate between the two codes, you can bypass this control.

### Lab: Infinite money logic flaw

This lab has a logic flaw in its purchasing workflow.

Sign up to our newsletter! -> Use coupon SIGNUP30 at checkout!

There is a gift card as a product.

If we can combine discount coupon and gift card, we can get infinite money logic flaw.

Verified - It works! -Every step, we can gain $3.

We can create a macro to automize this process.

For detailed information of creating the macro, you can see the solution of the lab.

Sequence of request:&#x20;

* POST /cart
* POST /cart/coupon
* POST /cart/checkout
* GET /cart/order-confirmation?order-confirmed=true
* POST /gift-card

We need to change gift card code to related request.

Then we start a sniper attack with GET / request with 412 null payloads.

When the attack stops, we will have enough money to buy the product.
