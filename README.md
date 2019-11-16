
# Derive Insight from NDS Using Methods...Repeated

## Learning Goals

* Create an "Nth-Order" method

## Introduction

In the previous lab we saw how "First-Order" methods make it easier for humans
to reason about code.

However, we can create code that is easier to maintain, clearer and more
beautiful if we wrap "First-Order" methods inside of _other methods_. In the
same way that we nested "raw" `[]`,  `Hash`, and `Array` methods _inside_
"First-Order" methods, we can nest "First-Order" methods _inside_ other
methods. We're going to call those Nth-Order methods.

"Make Breakfast" is an Nth-Order method in Real Life. It contains "Make toast"
and "Make coffee." Each of those have multiple methods like "Get Bread. Toast
Bread. Apply butter to toasted bread. Etc."

We brought in this idea of "Order" as a teaching to help encourage you to see
how to "wrap" methods. The _technique_ is what you should take away, not the
label. Wrapping low-level methods with nicely-named, abstract methods is a good
thing.

When code is structured this way, there are many benefits:

* It's easier to debug
* It's more obvious to read
* It's more easy to get help with online

## Starting Point

First, let's start with some "starter" code.

```ruby
vm = [[[{:name=>"Vanilla Cookies", :price=>3}, {:name=>"Pistachio Cookies", :price=>3}, {:name=>"Chocolate Cookies", :price=>3}, {:name=>"Chocolate Chip Cookies", :price=>3}], [{:name=>"Tooth-Melters", :price=>12}, {:name=>"Tooth-Destroyers", :price=>12}, {:name=>"Enamel Eaters", :price=>12}, {:name=>"Dentist's Nightmare", :price=>20}], [{:name=>"Gummy Sour Apple", :price=>3}, {:name=>"Gummy Apple", :price=>5}, {:name=>"Gummy Moldy Apple", :price=>1}]], [[{:name=>"Grape Drink", :price=>1}, {:name=>"Orange Drink", :price=>1}, {:name=>"Pineapple Drink", :price=>1}], [{:name=>"Mints", :price=>13}, {:name=>"Curiously Toxic Mints", :price=>1000}, {:name=>"US Mints", :price=>99}]]]

def total_value_of_spinner(nds, row_index, column_index)
  coordinate_total = 0
  inner_len = nds[row_index][column_index].length
  inner_index = 0
  while inner_index < inner_len do
    coordinate_total += nds[row_index][column_index][inner_index][:price]
    inner_index += 1
  end
  coordinate_total
end

# Main code

grand_total = 0
row_index = 0
while row_index < vm.length do
  column_index = 0
  while column_index < vm[row_index].length do
    grand_total += total_value_of_spinner(vm, row_index, column_index)
    column_index += 1
  end
  row_index += 1
end

# End Main code

p grand_total #=> 1192
```

## Create an "Nth-Order" method

Looking at the starter code, the main loop is _still_ not so easy to reason
about. Let's zoom in to it:

```ruby
# Non-runnable
grand_total = 0
row_index = 0
while row_index < vm.length do
  column_index = 0
  while column_index < vm[row_index].length do
    grand_total += total_value_of_spinner(vm, row_index, column_index)
    column_index += 1
  end
  row_index += 1
end
```

That inner-most `while` _means_ "go to each spinner on the row, and total it
up".  That is, "**total up the row based on its spinners** by use of
`total_value_of_spinner`" Wouldn't it be GREAT if there were a method called
`total_snack_value_of_row`? Again, there's nothing stopping us from making it!
All we have to do to total a row's snack count is add together the snack counts
of its spinners.

Let's update the code:

```ruby
vm = [[[{:name=>"Vanilla Cookies", :price=>3}, {:name=>"Pistachio Cookies", :price=>3}, {:name=>"Chocolate Cookies", :price=>3}, {:name=>"Chocolate Chip Cookies", :price=>3}], [{:name=>"Tooth-Melters", :price=>12}, {:name=>"Tooth-Destroyers", :price=>12}, {:name=>"Enamel Eaters", :price=>12}, {:name=>"Dentist's Nighmare", :price=>20}], [{:name=>"Gummy Sour Apple", :price=>3}, {:name=>"Gummy Apple", :price=>5}, {:name=>"Gummy Moldy Apple", :price=>1}]], [[{:name=>"Grape Drink", :price=>1}, {:name=>"Orange Drink", :price=>1}, {:name=>"Pineapple Drink", :price=>1}], [{:name=>"Mints", :price=>13}, {:name=>"Curiously Toxic Mints", :price=>1000}, {:name=>"US Mints", :price=>99}]]]

def total_snack_price_on_spinner(nds, row_index, column_index)
  coordinate_total = 0
  inner_len = nds[row_index][column_index].length
  inner_index = 0
  while inner_index < inner_len do
    coordinate_total += nds[row_index][column_index][inner_index][:price]
    inner_index += 1
  end
  coordinate_total
end

def total_snack_value_of_row(nds, row_index)
  grand_row_total = 0
  column_index = 0
  while column_index < nds[row_index].length do
    grand_row_total += total_snack_price_on_spinner(nds, row_index, column_index)
    column_index += 1
  end
  grand_row_total
end

grand_total = 0
row_index = 0
while row_index < vm.length do
  grand_total += total_snack_value_of_row(vm, row_index)
  row_index += 1
end

p grand_total #=> 1192
```

## Wrap an Additional Method

Suddenly all that's left in our original kinda-messy "raw" implementation  is
the following:

```ruby
# Non-Runnable

grand_total = 0
row_index = 0
while row_index < vm.length do
  grand_total += total_snack_value_of_row(vm, row_index)
  row_index += 1
end
```

But this bit of code has a purpose. What does this code _mean_? What is it
trying to accomplish?

Isn't it "sum up the values of all the rows" the same thing as seeking
"`total_value_of_snacks_in_machine`?" Just as before, we're going to "wrap"
`total_snack_value_of_row` into a new method: `total_value_of_snacks_in_machine`.

```ruby
vm = [[[{:name=>"Vanilla Cookies", :price=>3}, {:name=>"Pistachio Cookies", :price=>3}, {:name=>"Chocolate Cookies", :price=>3}, {:name=>"Chocolate Chip Cookies", :price=>3}], [{:name=>"Tooth-Melters", :price=>12}, {:name=>"Tooth-Destroyers", :price=>12}, {:name=>"Enamel Eaters", :price=>12}, {:name=>"Dentist's Nighmare", :price=>20}], [{:name=>"Gummy Sour Apple", :price=>3}, {:name=>"Gummy Apple", :price=>5}, {:name=>"Gummy Moldy Apple", :price=>1}]], [[{:name=>"Grape Drink", :price=>1}, {:name=>"Orange Drink", :price=>1}, {:name=>"Pineapple Drink", :price=>1}], [{:name=>"Mints", :price=>13}, {:name=>"Curiously Toxic Mints", :price=>1000}, {:name=>"US Mints", :price=>99}]]]

def total_snack_price_on_spinner(nds, row_index, column_index)
  coordinate_total = 0
  inner_len = nds[row_index][column_index].length
  inner_index = 0
  while inner_index < inner_len do
    coordinate_total += nds[row_index][column_index][inner_index][:price]
    inner_index += 1
  end
  coordinate_total
end

def total_snack_value_of_row(nds, row_index)
  grand_row_total = 0
  column_index = 0
  while column_index < nds[row_index].length do
    grand_row_total += total_snack_price_on_spinner(nds, row_index, column_index)
    column_index += 1
  end
  grand_row_total
end

def total_value_of_snacks_in_machine(nds)
  grand_total = 0
  row_index = 0
  while row_index < nds.length do
    grand_total += total_snack_value_of_row(nds, row_index)
    row_index += 1
  end
  grand_total
end

p total_value_of_snacks_in_machine(vm) #=> 1192
```

Wow! That's really handy! Instead of one triply-nested `while` loop, we now
have **three** helpful methods that will help hide away complexity and keep our
code readable and maintainable.

From the perspective of generating _insight_, our code knows what _insight_
**we** want and what _insight_ **it is ready to share**. This code makes it
easy to find by calling a method whose name immediately suggest the _insight_
we want: `total_value_of_snacks_in_machine`. The method then, internally, uses
_other_ methods to help keep itself clean and simple:

```text
total_value_of_snacks_in_machine
 \-> total_snack_value_of_row
    \-> total_snack_price_on_spinner
```

## Growing the Code

Looking at the three methods we have to process this NDS, if we wanted to
figure out the _insight_ of `average_price_per_snack` we could take
`total_value_of_snacks_in_machine` and divide it by the (not-yet written)
`total_number_of_snacks`. That really wouldn't be that much work at all.

By creating clean, abstract, well-named, and small methods that use each other,
we can grow the features of our code while keeping it clean and understandable.

## Lab

In the lab you're going to write a method that uses First-Order methods to do
its work.

## Conclusion

Whenever you have complex data that needs to be "transformed," "massaged," or
"changed," let _methods_ help your human brain keep track of the constituent
actions that need to happen to create the _insights_ you need. Compare this
last bit of code with the first "raw `[]` and `Array` methods" implementation.
Which version would _you_ rather debug? Which is easier to talk about and ask
for help with?

What's even more interesting, is that as your collection of "First-Order"
methods grows, you'll find yourself writing methods that mix their outputs
together to create new _insights_. Eventually, you might have so many
well-written, tiny, clear "First-Order" methods that reasoning about,
explaining, and extending your code is simple. That's the goal.
