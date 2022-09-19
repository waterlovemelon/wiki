# Qt

## QSpacerItem的changeSize方法不生效

如果QSpacerItem对象已经加入到了一个layout中，那么在changeSize后需要调用一下layout的invalidate 方式。下面是qt对QSpacerItem的changeSize方法的说明：

Changes this spacer item to have preferred width w, preferred height h, horizontal size policy hPolicy and vertical size policy vPolicy.

The default values provide a gap that is able to stretch if nothing else wants the space.

*Note that if changeSize() is called after the spacer item has been added to a layout, it is necessary to invalidate the layout in order for the spacer item's new size to take effect.*

## 设置dbus属性

### 设置属性

使用`org.freedesktop.DBus.Properties`的set方法，需要把value转成QDBusVariant才能调用成功，否则dbus会报错：找不到“Set”方法

### 关联PropertiesChanged信号

```C++
QDBusConnection::systemBus().connect(dbus_name,
                                          dbus_path,
                                          "org.freedesktop.DBus.Properties",
                                          "PropertiesChanged",
                                          m_model, SLOT(onUpdatePropertiesChanged(QString,QVariantMap,QStringList)));
public slots:
    void onUpdatePropertiesChanged(const QString &interfaceName,
                                 const QVariantMap &changedProperties,
                                 const QStringList &invalidatedProperties);
```

## 日志级别

配置文件位置：~/.config/QtProject.conf

```ini

[Rules]
%2A.debug=true
qt.qpa.input%2A.debug=false
qt.scaling.debug=false
qt.widgets.gestures=false


```