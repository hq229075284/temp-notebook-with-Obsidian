## [Flexbox and other layout methods](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Relationship_of_Flexbox_to_Other_Layout_Methods#flexbox_and_other_layout_methods)

1. An element set to `display: flex` behaves in most ways like any other block level container that establishes a containing block. Floats will not intrude, and the containers' margins will not collapse.
2. if an item was floated or cleared and then becomes a flex item due to the parent having `display: flex` applied, the floating and clearing will no longer happen, and the item will not be taken out of normal flow in the way that floats are.


## [Flexbox and Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Relationship_of_Flexbox_to_Other_Layout_Methods#flexbox_and_grid_layout)


## [The Cross Axis](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Aligning_Items_in_a_Flex_Container#the_cross_axis)

 If your flex container has a height set, then the items will stretch to that height, regardless of how much content is in the item.


## [Using auto margins for main axis alignment](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Aligning_Items_in_a_Flex_Container#using_auto_margins_for_main_axis_alignment)

we can target item 4 and separate it from the first three items by giving it a `margin-left` value of `auto`. Auto margins will take up all of the space that they can in their axis — it is how centering a block with margin auto left and right works.

## [Flex item sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Controlling_Ratios_of_Flex_Items_Along_the_Main_Ax#flex_item_sizing)

The first paragraph has a width of `min-content`. You should be able to see that the text has taken all of the soft wrapping opportunities available to it, becoming as small as it can be without overflowing. This then, is the `min-content` size of that string. Essentially, the longest word in the string is dictating the size.

The second paragraph has a value of `max-content` and so it does the opposite. It gets as big as it possibly can be, taking no soft-wrapping opportunities. It would overflow the box it is in if that container was too narrow.

## [The flex-basis property](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Controlling_Ratios_of_Flex_Items_Along_the_Main_Ax#the_flex-basis_property)

If your item is instead auto-sized, then `auto` resolves to the size of its content. Flexbox will take the `max-content` size of the item as the `flex-basis`.

If you want flexbox to completely ignore the size of the item when doing space distribution then set `flex-basis` to `0`.

## [Combining `flex-shrink` and `flex-basis`](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Controlling_Ratios_of_Flex_Items_Along_the_Main_Ax#combining_flex-shrink_and_flex-basis)

Flexbox prevents small items from shrinking to zero size during this removal of negative free space.The items will be floored at their `min-content` size




