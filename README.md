# Supermaket Sweep

I had a lot of fun working through this problem with design and the questions were nice to iterate on.

In my first iteration, I broke down the problem using a pretty standard relational database. I've put the basic models and classes below.

_Note: All prices are represented using cents. Ex: $1.49 = 149_

Here's a simple Product model to create the grocery item. Note the relationship with Discount.
```
class Product < ApplicationRecord
	# name:string
	# price:integer
    has_many :discounts
end
```
I distilled the pricing rule down to a pretty generic form. I noted that the %50 off can just be a pre-calculated dollar amount. The rule "Buy 2 packets of barilla penne pasta and get 1 for 50% off" can just be calculated "Buy 3 packets of barilla penne pasta and get (Barilla penne pasta.price) / 2"
> Prerequisite: Scan (n) of product A, get X dollars off

That equation worked well until the "Buy a can of milk and get a box of Hershey's for half off" rule.

```
class Discount < ApplicationRecord
    # name:string
    # discount:integer (cents)
    # 
    belongs_to :product
end
```


