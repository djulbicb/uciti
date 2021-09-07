#### Plugins

Lombok, MapStruct Support, Axis TCPMon ali ga nema vise - za pracanje requesta u Inteliju
File | Settings | Build, Execution, Deployment | Compiler | Annotation Processors > Enable annotation processor

#### Project structure

```
Beer consumer - creates demand for beer
kotlin, webclient

Pub - provides beer to beer consumer
webflux.fn, spring data mongo, mongo db

Distributor - restocks beer to pubs, orders beer from brewery. 
webflux, mongo data, mongoDB

Brewery - brews beer, supplies beer to distributor
spring mvc, spring data jpa, hibernate
```

#### Init project

```
git init
group: guru.springframework
artifact: massc-brewery
name: masc-brewery
description: SFG Brewery
package name: guru.springframerwork.masschbewery
packaging: jar
java version: 11
dependencies: Web, Lombok, Devtools, Actuator
```





### Validator

```
@Null @NotNull @AsserTrue @AsserFalse @Min @Max
@DecimalMin @DecimalMax @Negative @NegativeOrZero @Positive @PositiveOrZero @Size
@Digits @Past @PastOrPresent @Future @FutureOrPresent @Pattern
@NotEmpty null or empty, whitespace is cool
@NotBlank 
@Email
@ScriptAssert, to test entire class
@CreditCardNumber
@Currentcy
@DurationMax
@DurationMin
@EAN
@ISBN
@Length - string length
@CodePoiuntLength
@LuhnCheck
@Mod10Check
@Range
@SafeHtml
@Url
@UniqueElements
@CNPJ @CPF @TituloEleitoral @Nip @PESEL @REGON

```









Response from postman

```
localhost:8080/api/v1/beer/06d84790-ff5b-4073-8395-8c41b4289005
{
    "uuid": "06d84790-ff5b-4073-8395-8c41b4289005",
    "beerName": "Some beer",
    "type": "Some type",
    "upc": null
}
```



Live reload, devtools triggers on build project and save template



Versionionig of API

semflow github
https://github.com/lyndseypadget/semflow