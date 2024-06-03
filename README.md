# Supermarket Sweep

I had a lot of fun working through this problem with design and the more thought I put into the questions, the better they became. Lots of edge cases and optimizations to consider.

In my first iteration, I broke down the problem to the basics.  I started by distilling the pricing rule down to a pretty generic form. I noted that %50 off can just be a pre-calculated dollar amount. The rule "Buy 2 packets of barilla penne pasta and get 1 for 50% off" can just be calculated "Buy 3 packets of barilla penne pasta and get (Barilla penne pasta.price) / 2".

That lead me to this calculation:

> Scan (n) of product A, get X dollars off

There was one problem though, this rule "Buy a can of milk and get a box of Hershey’s for half off". That needed a different discount calculation:

> Scan required product_ids, get X dollars off

I felt that was a good place to start so I created some basic models and calculations.

_Note: All prices are represented using cents. Ex: $1.49 = 149_

```ruby
class Product < ApplicationRecord
    # name:string
    # price:integer
end
```
```ruby
class Discount < ApplicationRecord
    # name:string
    # discount:integer (cents)
    # required_items:[product_ids...]
end
```
Here's the calculation to run on each scan (All discounts would be stored in memory)

_Note: Discounts should be ordered by discount from largest to smallest because we're not Kroger and we want to give the best discount_
```
def checkDiscounts(scannedItemIds, discounts):
    discounts.each do |discount|
        return discount if (discount.required_items - scannedItemIds).empty?
    end
end
```

While I think this would be able to calculate the discount, the order of the scanning would play into what discounts get applied. To give the best discount with the current discount function, we would need to run every combination of discounts for the given scanned items each scanned.

I thought this solution would be too slow and too cumbersome to manage. Having to specifically apply discounts to set number of products can work, but I'm lazy and I assume others are too. I want to just be able to create a groups of items to discount. Let's say we want to create a discount for all of one brand, or all soda products, or a combination of those, all soda products from a given brand.

The current system doesn't work well enough.

Here's a few other rules that current grocery stores use:

1. Multiples of 2, get discount
2. Buy one, get another of equal or lesser value for free
3. All 2-liters of Coca-Cola products are discounted %50 off

The current system cannot do these. For rule #2, that discount is based off of the current value of an item, not a set discount. Rule #3, not all of those products would be the same price, so the calculation would need to be done on the fly. Plus adding a new Coca-Cola product would be so annoying.

We need a better rule engine, plus an easy way to manage discounts. Enter: ProductGroups.

More info to come in version 2.
