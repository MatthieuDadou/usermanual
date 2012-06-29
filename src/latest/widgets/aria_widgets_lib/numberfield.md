Title: NumberField



NumberField widget enables the creation of input fields that should contain a numeric value. It also provides formatting facilities to handle numeric and currency patterns.

<srcinclude tag="wgtNumberField" lang="AT" outdent="true">widgets/numberfield/Snippet.tpl</srcinclude>

The whole list of configuration parameters is available in [NumberFieldCfg bean](http://ariatemplates.com/aria/guide/apps/apidocs/#aria.widgets.CfgBeans:NumberFieldCfg).

## Formatting patterns

You can use formatting patterns to specify how a number should be conventionally displayed, even as a currency. It can be defined as a combination of the following:

{| style="width:100%"
|-
! align="left" style="border-bottom: 1px solid black" | Symbol
! align="left" style="border-bottom: 1px solid black" | Location
! align="left" style="border-bottom: 1px solid black" | Meaning
|-
| 0
| Number
| Digit
|-
| #
| Number
| Digit, zero shows as absent
|-
| .
| Number
| Decimal separator
|-
| ,
| Integer
| Grouping separator
|-
| ¤ (\u00A4)
| Prefix or suffix
| Currency sign, replaced by currency symbol.
|}

The supported patterns are a subset of the [java.text.DecimalFormat](http://download.oracle.com/javase/1.4.2/docs/api/java/text/DecimalFormat.html) specs. More information are available at [Localization and Resources](Localization_and_Resources).

## Sample usage

The following sample shows how to create and use a NumberField widget, with a mandatory attribute for error checking and number format patterns. In order to fetch the entered value, variable binding is to be considered as the preferred solution. Please refer to the [TextField](TextField) widget for more informations.

<sample sample="widgets/numberfield" />

In this sample, the first 3 NumberFields are the same except for the state, which is respectively mandatory, normal and disabled.
The fourth NumberField has a pattern, that is used both to interpret and display the number.

More information about the pattern are available at [Localization and Resources](Localization_and_Resources).