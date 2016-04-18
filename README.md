#### psbuiscaapiawsgatew
#####concept AWS API gateway
settings->config per stage (reduce traffic, 0.5g-237g)
#####grant user access
######create interpreting access policies
```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
      "apigateway:GET",
      "apigateway:POST"
      ],
      "Resource":[
      ]
    }
  ]
}
```
format(resource)
```
arn:aws:apigateway:region::specifier   //more specific,
arn:aws:apigateway:region::api-id/stage-name/resource-path-specifier
arn:aws:apigateway:*::*:*  //anyresource ,any stage, any api, any aws region
```
#####using lambda
first function
```
exports.handler = function(event,context){
    var input1 = (event.input1 === undefined?'k':event.input1);
     var input2 = (event.input2 === undefined?'e':event.input2);
     var response = input1+input2;
     context.done(null,{"response":response});
}
```
######basic policy
```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource":"arn:aws:logs:*:*:*"
    }
  ]
}
```
invocation policy
```
{
  "Statement":{
  "StatementId":"Id-1",
  "Action":"lambda:InvokeFunction",
  "Principal":"apigateway.amazonaws.com",
  "Sourcearn":"arn:aws:execute-api:apipath",
  "SourceAccount":"id"
}
}
```
#####build resource
go to api gateway, create resource add lambda function. test   
select resource, actions->add method, select GET.  
select (method response)  
add response headers for 200,
```
Access-Control-Allow-Headers
Access-Control-Allow-Methods
Access-Control-Allow-Origin
```
select (Integration Response) arrow->Header Mappings
```
Access-Control-Allow-Headers -> 'Content-Type,X-Amz-Date,Authorzation'
Access-Control-Allow-Methods -> 'GET,POST'
Access-Control-Allow-Origin -> '*'
```

#####build second
######function
```
exports.handler = function(event,context){
    var o = {};
    o.id=event.id;
    o.name=(Math.floor(Math.random()*30)+10).toString();
    context.succeed(o);
}
```
go to api gateway, create resource add lambda function. test   
select resource, actions->add method, select POST.  
test: add body  
```
{"id":1}
```
######Adding URL param
```
$context.apiId
$context.identity.accountOwner
$context.identity.sourceIp
```
```
$input.json(x)
$input.params()
$input.path(x)
```
Ex:
```
POST /x
{
"a":{
  "a1":"b1"
}
}
```
equalto
```
$input.path('$.a').size()
```
$util:using in mapping templates
```
$util.escapeJavascript()
$util.urlEncode()
$util.urlDecode()
$util.base64Encode()
$util.base64Decode()
```
Ex:
```
/o/{x}  -> {"id":"$input.params('x')"}
```
