# GoReports
Data reports generator

## Basic Description

This is standalone program that offer web service about generating reports.
Type of reports generated from GoReports:
- PDF
- Excel
- XML

## Report Dir Structure

Директория, която се съдържат справките на приложението:
- dir
  - foconf
  - root
  - sql
  - document.xml
  - src.xml
  
##### foconf

Съдържа стиловете и шрифтовете на генерираните документи, които се извикват в xsl трансформациите

##### root

Съдържа xsl трансформациите на приложението (вложеноста 
и влагането на отделни нива и групи е неограничено, защото конкретна справка се посочва с отнодителен път, за чието начало се взима dir/root).

##### sql

Съдържа sql заявките към базата, чрез които се извличат данните, които ще коструират данните.(тук също няма ограничение ан вложеност в различни по-категории с някаква лична коструираща логика)

##### document.xml

В него се съдържат връзките между името на спраквата, типа на резултата от спраквата, описание на справката, xsl трансформацията и sql заявките на спраквата и имената на параметрите подавани на справката.

#### Пример:
```
<?xml version='1.0' encoding='windows-1251'?>
 <root>
  <section description="Excel Reports">
	  <type code="EMPLOYEE.SALARY.SIMPLE">
		  <description>Deployee salary</description>
		    <xslt output="fop">hr/employees_salary_simple.xsl</xslt>
        <statements>
			     <item rowset="list">hr/employees_salary_simple</item>
        </statements>
 	     <params>
          <param code="P_DATE_FROM"/>
          <param code="P_DEPARTMENT"/>
        </params>
   </type>
    <type code="...">
      ......
    </type>
 </section>
 <section description="...">
 ....
 </section>
  ....
 </root>
```
##### section

логическо разделяне на отделните типове справки(един document.xml може да има много такива)

##### type

в него се съдържа описанието на дадена справка. Атрибутът code се използва при извикване на програмата, за оказване коя е 
желаната справка. Кодът на справката не е необходимо да бъде уникален. Необходимо е комбинацията code и output аргумента на тага xslt да бъдат уникални.

##### desctricption

описание на справката

##### xslt

в него се задава типа на очаквания резултат(чрез атрибута output) и пътя до xsl трансформацията като се използва относителен път до файла, който се намира в root папката.

##### statements

съдържа отделните sql заявките на данните.

  ##### item
    
   в него се съдържат относителните пътища до sql справката и името, в която ще ги достъпваме с xls транформацията.
    
##### params

съдържа имената на параметрите на справката.

  ##### param
  
   чрез атрибута code се задава името на параметъра, което ще се използва за мапинг при получаване на заявката и също в sql заявките и xsl трансформацията.
    
##### src.xml

В този файл се съдържат connection стринговите на отделните бази с паролите и имената, чрез които съответно при правене на една справка ще е извличат данните.
Пример:

```
 <root>
  <datasource name="HR-name">
   <db-src>jdbc:oracle:thin:@GalacticAC:1521:xe</db-src>
   <db-usr>HR</db-usr>
   <db-psw>HR</db-psw>
  </datasource>
  <datasource name="...">
   ...
  </datasource>
 </root>
```

root - в него се съдържат отделните заявки бази.
database - има атрибут name, който се изпозлва на укално и се използва за определяне на базата в подадената заявка за справка.

##### Common

Програмата приема заявки, който съдържат
  - src - name of database
  - report-code - code of report
  - report-type - type of generated result (pdf,excel и т.н.)
  - Param1=&...&ParamN= - report params, where Param1 is a name of param that is declared in document.xml docuname and this param will be initializе with paressed value and Param2 .... and so on.
  
 След прочитане на данните от декларираните sql заявки в document.xml се генерира следния примерен xml документ

```
 <root>
  <params>
   <db-src>jdbc:oracle:thin:@GalacticAC:1521:xe</db-src>
   <db-usr>HR</db-usr>
   <db-psw>HR</db-psw>
   <report-code>HR</report-code>
   <return-type>excel</return-type>
   <Param-1></Param-1>
   ...
   <Param-n></Param-n>
  </params>
  <row>
   <employee>
    <id>3</id>
    <name>Rafi</name>
    <age>29</age>
   </employee>
   <employee>
   ...
   </employee>
   <department>
    <id>13</id>
    <name>HR</name>
    <location>Paris</location>
   </department>
   ...
  </row>
 </root>
```
root - корена на генерирания докумнет
params - съдържа параметрите на справката (като името на базата, подадените параметри,името на справката и т.н.)
row - съдържа данните върнати от sql заявките, където в примера employee e името декларирано на съответната sqlзаявка в document.xml, елементите id, name, age са имената на колоните върнати от съответната заявка. Резултатите от заявките са в таг, чието име отговаря на декларирането, и резултатите върнати от всяка зявка са в ред съответен на реда на декларация в document.xml(т.е. може да имаме 5 пъти тага employee след това 2 пъти данните за department)
