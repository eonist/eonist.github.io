Spec for saving gradient information for the LineStyle and FillStyle 

```xml
<linestyle thickness="10.0">
	<gradient type="linear" rotation="1.14">
		<stop color="#FF00FF" alpha="1.0"/>
		<stop color="#0000FF" alpha="1.0"/>
	</gradient>
</linestyle>

```

- a linestyle with no color and a child that is a linear or radial gradient should use the gradient. 
- if you add an offset then that counts, no offset it uses some math to calc the offset
- The gradient is a child of linestyle xml element (using)
- The gradient for fill is a child of the fillstyle xml element
- offsets are from 0-1 and are delimted with a comma
- rotation use values from 3.14 to -3.14