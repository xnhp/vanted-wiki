Graph Element Attributes are organised in a tree structure. For example, some attributes that can be found on a node are...

- `graphics` of type `*NodeGraphicAttribute*`
    - coordinate
    - dimension
    - fill
    - opacity
    - ...
- `labelgraphics` of type `*NodeLabelAttribute*`

    (only present if the graph element indeed has a label)

    - alignment
    - fontSize
    - text
    - ...

Note that attributes themselves do not describe any drawing routine. This is done via `NodeComponent` / `EdgeComponent` and attribute components that access these attributes.

# Accessing Attributes

See [this wiki page](APIOverviewExamples).

# Attributes and Attribute Components

To an attribute, we can assign an attribute component. This attribute component (amongst other things) describes a draw routine. This can be used, for example, to draw the label of a node.

An attribute component is not limited to the information its corresponding attribute holds. 

Indeed, it is common in Vanted to implement additional visualisations to a graph element by creating 

- an attribute that holds little to no information
- and a corresponding attribute component that accesses (and visualises) information from other sources. To this end, `StringAttribute` can be used.

Attributes are always of a specific subclass of `AbstractAttribute`.

The steps needed to display a custom attribute are as follows:

```java
// register a color map attribute
this.attributes = new Class[1];
this.attributes[0] = ColorMapAttribute.class;

// register the color map attribute as a string attribute
// A String attribute is simply an attribute that contains a string.
StringAttribute.putAttributeType(ColorMapAttribute.name, ColorMapAttribute.class);

// link the attribute and the attribute component
attributeComponents.put(ColorMapAttribute.class, ColorMapAttributeComponent.class);
```

See:
* `LabelAttribute` and `LabelComponent`
* `StarAttribute` and `StarAttributeComponent` (in the example Add-On)
* Example-Addon, [as described in the wiki](AddonExtensions)
