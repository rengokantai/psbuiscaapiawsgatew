# psbuiscaapiawsgatew
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
