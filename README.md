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
