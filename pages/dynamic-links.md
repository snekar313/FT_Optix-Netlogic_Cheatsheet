# Dynamic Links

## Simple Dynamic Link

By default a `DynamicLink` is made as _Read only_ unless you specify different mode

```csharp
var myObj = Owner.Get<Motor>("Motor1");
var speedValue = Owner.GetObject("SpeedLabel").GetVariable("Text");
myObj.SpeedVariable.SetDynamicLink(speedValue, DynamicLinkMode.Read);
```

### Dynamic Link to a bit indexed word

```csharp
[ExportMethod]
public void AddDynamicLinkToBitOfIntegerVariable()
{
    var myBitAlarm = Project.Current.GetVariable("Model/BitVariable");
    IUAVariable tempVariable = null;
    myBitAlarm.SetDynamicLink(tempVariable, DynamicLinkMode.ReadWrite);
    myBitAlarm.GetVariable("DynamicLink").Value = "../Int32Variable.1";
}
```

## Creating a String Formatter

```csharp
var variable4 = InformationModel.MakeVariable("Variable4", OpcUa.DataTypes.String); 
Project.Current.Get("Model").Add(variable4); 
var stringFormatter = InformationModel.Make<StringFormatter>("StringFormatter1"); 
stringFormatter.Format = "{0} and {1}"; 
var source0 = InformationModel.MakeVariable("Source0", OpcUa.DataTypes.BaseDataType); 
stringFormatter.Refs.AddReference(FTOptix.CoreBase.ReferenceTypes.HasSource, source0); 
var source1 = InformationModel.MakeVariable("Source1", OpcUa.DataTypes.BaseDataType); 
stringFormatter.Refs.AddReference(FTOptix.CoreBase.ReferenceTypes.HasSource, source1); 
variable4.SetConverter(stringFormatter); var variable1 = Project.Current.GetVariable("Model/Variable1"); 
var variable2 = Project.Current.GetVariable("Model/Variable2"); 
source0.SetDynamicLink(variable1); 
source1.SetDynamicLink(variable2);
```

## Creating an Expression Evaluator

```csharp
[ExportMethod]
public void Method1()
{
    var var1 = Project.Current.GetVariable("Model/Variable1");
    var var2 = Project.Current.GetVariable("Model/Variable2");
    var var3 = Project.Current.GetVariable("Model/Variable3");

    var expressionEvaluator = InformationModel.MakeObject<ExpressionEvaluator>("ExpressionEvaluator");
    expressionEvaluator.Expression = "{0} + {1}";

    var source0 = InformationModel.MakeVariable("Source0", OpcUa.DataTypes.BaseDataType);
    source0.SetDynamicLink(var2);
    var source1 = InformationModel.MakeVariable("Source1", OpcUa.DataTypes.BaseDataType);
    source1.SetDynamicLink(var3);

    expressionEvaluator.Refs.AddReference(FTOptix.CoreBase.ReferenceTypes.HasSource, source0);
    expressionEvaluator.Refs.AddReference(FTOptix.CoreBase.ReferenceTypes.HasSource, source1);

    var1.SetConverter(expressionEvaluator);
}
```

## Creating a Key-Value Converter
```csharp
private void StuffCreateKeyPair(IUAVariable targetNode, IUAVariable sourceVariable)
{
    ValueMapConverter newValueMapConverter = InformationModel.MakeObject<ValueMapConverter>("KeyValueConverter1", FTOptix.CoreBase.ObjectTypes.ValueMapConverter);
    IUAObject newPairs = InformationModel.MakeObject("Pairs");
    for (int i = 0; i < 3; i++)
    {
        string pairBrowseName = "Pair";
        if (i > 0) pairBrowseName += i.ToString();
        IUAObject newPair = InformationModel.MakeObject(pairBrowseName, FTOptix.CoreBase.ObjectTypes.ValueMapPair);
        IUAVariable newKey = newPair.GetVariable("Key");
        IUAVariable newValue = newPair.GetVariable("Value");
        newKey.DataType = OpcUa.DataTypes.UInt32;
        newValue.DataType = FTOptix.Core.DataTypes.Color;
        newKey.Value = i;
        newValue.Value = System.Drawing.Color.Violet.ToArgb();
        newPairs.Add(newPair);
    }
    newValueMapConverter.Add(newPairs);
    newValueMapConverter.Mode = DynamicLinkMode.Read;
    newValueMapConverter.SourceVariable.SetDynamicLink(sourceVariable);
    targetNode.SetConverter(newValueMapConverter);
}
```

## Creating a Conditional Converter
```csharp
private void StuffCreateConditionaConverter(IUAVariable targetNode, IUAVariable sourceVariable)
{
    ConditionalConverter newConditionalConverter = InformationModel.MakeObject<ConditionalConverter>("ConditionalConverter1", FTOptix.CoreBase.ObjectTypes.ConditionalConverter);
    newConditionalConverter.FalseValueVariable.DataType = FTOptix.Core.DataTypes.Color;
    newConditionalConverter.FalseValueVariable.Value = System.Drawing.Color.Red.ToArgb();
    newConditionalConverter.TrueValueVariable.DataType = FTOptix.Core.DataTypes.Color;
    newConditionalConverter.TrueValueVariable.Value = System.Drawing.Color.Green.ToArgb();
    newConditionalConverter.ConditionVariable.SetDynamicLink(sourceVariable);
    newConditionalConverter.Mode = DynamicLinkMode.Read;
    targetNode.SetConverter(newConditionalConverter);
}
```
