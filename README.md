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
create an IAM role, attach this policy
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
invocation policy,as relationship policy
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
create new resource 's'
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
create new resource 'o'
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
######building order map template
create child resource under o.  
resourcename:x  
resourcepath: /o/{x} create new method GET,lambda function:b  
Integration Request->add mapping templates,add
```
application/json ,template {"id":"$input.params('x')"}
```
Then test: type 1 to test
######deployment
choose root->deploy api->[new stage]->name:stage  
enable cloudwatch logs->loglevel:info,log full req/res,Enable CloudWatch Metrics  
burstlimit=200 rate=500  
donot forget to add cloudwatch arn to settting. [see here](http://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-stage-settings.html)
######postman
Test s, using GET
```
https://z.us-east-1.amazonaws.com/stage/s
```
Test o using POST with body
```
https://z.us-east-1.amazonaws.com/stage/o
```
```
{"id":"1"}
```
or using curl: test post:
```
curl -XPOST https://z.us-east-1.amazonaws.com/stage/o -d '{"id":"1"}'
```
