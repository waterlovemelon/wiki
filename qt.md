# Qt

## QSpacerItem的changeSize方法不生效

如果QSpacerItem对象已经加入到了一个layout中，那么在changeSize后需要调用一下layout的invalidate 方式。下面是qt对QSpacerItem的changeSize方法的说明：

Changes this spacer item to have preferred width w, preferred height h, horizontal size policy hPolicy and vertical size policy vPolicy.

The default values provide a gap that is able to stretch if nothing else wants the space.

*Note that if changeSize() is called after the spacer item has been added to a layout, it is necessary to invalidate the layout in order for the spacer item's new size to take effect.*
