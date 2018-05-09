# PSHTML
Module to generate HTML markup language within a DSL

# Example

PSHTML allow people to write a HTML document using powershell-like syntax, which makes building a webpage easier, and less cumbersome.

## A few Basic examples

### Basic page

```Powershell

html {

    head{

        title "woop title"
        link "css/normalize.css" "stylesheet"
    }

    body{

        Header {
            h1 "This is h1 Title in header"
            div {
                p {
                    "This is simply a paragraph in a div."
                }
            }
        }
    
        
            p {
                h1 "This is h1"
                h2 "This is h2"
                h3 "This is h3"
                h4 "This is h4"
                h5 "This is h5"
                h6 "This is h6"
                strong "plop";"Woop"
            }
    }
    
}

```

## A more advanced example:

```powershell
<#
    Tribute to Snover but added CSS styles and bootstrap.
    The following example called 'tribute to Snover' grasps information for the 'shellfather' from around the internet, adn generates a html page with the information.
    # This example ilustrates how we can leverage the power of powershell, to fetch and filter information on various websites (using invoke-webrequest)

    We use foreach to create multiple <p> or <li> elements.
#>

import-module .\pshtml.psd1

$Snover = Html {
    head -content {
        Title "Tribute to snover"
        #Linking to bootstrap

        $AllLinks = @()
        $AllLinks += @{
            "rel" = "stylesheet"
            "href" = "https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
            "Integrity" = "sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB"
            "crossorigin"= "anonymous"
        }

        $AllLinks += @{
            "rel" = "stylesheet"
            "href" = "https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" 
            "Integrity" = "sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp"
            "crossorigin"= "anonymous"
        }

        $ScriptParams = @()
        
        $ScriptParams += @{

            "src" = "https://code.jquery.com/jquery-3.3.1.slim.min.js"
            "integrity"="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8=" 
            "crossorigin"="anonymous"
        }

        $ScriptParams += @{

            "src" = "https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
            "integrity"="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" 
            "crossorigin"="anonymous"
        }

        $scriptParams += @{

            "src" = "https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" 
            "integrity"="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49"
            "crossorigin"="anonymous"
        }

        $scriptParams += @{

            "src" = "https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
            "integrity"="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T"
            "crossorigin"="anonymous"
        }

        foreach ($link in $AllLinks){
            link @link
        }


        foreach ($ScriptParam in $ScriptParams){

            script @ScriptParam
        }


        link -rel stylesheet -href "MyStyles.css"
    }
    Body{

        div -Class "container" {
            div -Class "text-center" {

                h1 "Tribute to Jeffrey Snover" -Class "Title"
    
                img -src "https://pbs.twimg.com/profile_images/618804166631145473/2q6yharL_400x400.jpg" -class "rounded-circle" -alt "Jeffrey Snover photo" -height "400" -width "400" 
                
        
            }
            div -id "Bio" {
                $WikiRootSite = "https://en.wikipedia.org"
                $Source = a {"Wikipedia"} -href $WikiRootSite
                h2 "Biography"
                h4 "Source --> $Source"
    
                #Gathering the biography information from Wikipedia
                $wiki = Invoke-WebRequest -Uri ($WikiRootSite + "/wiki/Jeffrey_Snover")
                $Output = $Wiki.ParsedHtml.getElementById("mw-content-text").children | ? {$_.ClassName -eq 'mw-parser-output'}
                $Bio = $Output.Children | ? {$_.TagName -eq 'p'} | select Tagname,InnerHtml
    
                foreach ($p in $bio){
                    if($p.InnerHtml -ne $null){
                        #The url are relative on Wiki website. Correcting it here so that the Links are still working
                        $Corrected = $p.innerHTML.Replace("/wiki/","$WikiRootSite/wiki/")
                        p{
    
                            $Corrected
                        }
                    }
                    
    
                }
                
            }#End Accomplishements
            Div -id "Snoverisms" {
                $SnoverismsSite = "http://snoverisms.com/"
    
                h2 "Snoverisms"
                h4 "Source --> $SnoverismsSite"
    
                $Page = Invoke-WebRequest -Uri $SnoverismsSite
                $Snoverisms = $Page.ParsedHtml.getElementsByTagName("p") | ? {$_.ClassName -ne "site-description"} | select innerhtml
                $Snoverisms += (Invoke-WebRequest -uri "http://snoverisms.com/page/2/").ParsedHtml.getElementsByTagName("p") | ? {$_.ClassName -ne "site-description"} | select innerhtml
    
                ul -id "snoverism-list" -Content {
                    Foreach ($snov in $Snoverisms){
                    
                        li -Class "snoverism" -content {
                            $snov.innerHTML
                        }
                    }
                    
                }#end of ul
            }
        }
    }
    Footer{
        $PSHTMLlink = a {"PSHTML"} -href "https://github.com/Stephanevg/PSHTML"
        h6 "Generated with $($PSHTMLlink)"
    }
}

$Snover > "Example6.html"
```

which results in the following HTML page:

![screen shot of PSHTML results](/Examples/Example6/tribute_snover.png)

## Templates

Since version 0.4 it is possible to build websites using templates. The following example showcase how this works:

```Example04/Example04.ps1``` contains the following sample code:

### Example04/Example04.ps1

```powershell


html{
    Header{
        h1 "This is an example generated using PSHTML Templates"
    }
    Body{

        include -name Body

    }
    Footer{
        Include -Name Footer
    }
}

```

Assuming that ```Example4/body.ps1``` and ```Example/Footer.ps1``` contains the following ```pshtml```code:

### body.ps1

```powershell

    h2 "This comes from a template file"

```

### footer.ps1

```powershell
div {
    h4 "This is the footer from a template"
    p{
        "Copyright from template"
    }
}
```

Would generate the following code:

```html

    <header>
        <h1>This is an example generated using PSHTML Templates</h1>
    </header>
    <body>
        <h2>This comes from a template file</h2>
    </body>
    <footer>
        <div>
            <h4>This is the footer from a template</h4>
        </div>
    </footer>
</html>

```
## Generating a (very) basic form

```PowerShell
form "MyPage.php" post _self -Content {

    input "text" "FirstName"
    input "text" "LastName"
    input submit "MySubmit"
}
```

Which generates the following code:

```html
<form action="MyPage.php" method="post" target="_self" >
    <input type="text" name="FirstName" >
    <input type="text" name="LastName" >
    <input type="submit" name="MySubmit" >
</form>
```
# Todo List

There is a lot to accomplish before making this module available to the public. 

I plan the following most important milestones:

 - [ ] Provide basic functionality (Generating an HTML document) using the DSL. The following sections are the highest priority:
    - [X] Root
    - [ ] Sections
    - [X] Tables
    - [ ] blocs
    - [ ] Forms
    - [ ] Textual semantic

In parallel to this, I want to add the support for the following attributes (as a first step):
- [ ] Class
- [ ] Id
- [ ] Style


Eventually, the following components will also be added:
 - [ ] Scripts
 - [ ] Include Sections
 - [ ] Interactive Data




## Root
- [X] ```<html>```
    - [ ] Add support for lang attribute
## MetdaData
- [X] ```<head>```
- [X] ```<title>```
- [ ] ```<base>```
- [X] ```<link>```
- [X] ```<meta>```
- [ ] ```<style>```

## Scripts

- [X] ```<script>```
- [X] ```<noscript>```	

## Sections
- [X] ```<body>```
- [X] ```<section>```
- [X] ```<nav>```
- [X] ```<article>```
- [X] ```<aside>```
- [X] ```<h1>```
- [X] ```<h2>```
- [X] ```<h3>```
- [X] ```<h4>```
- [X] ```<h5>```
- [X] ```<h6>```
- [ ] ~~```<hgroup>```~~ 'Functionallity still in beta'
- [X] ```<header>```
- [X] ```<footer>```
- [X] ```<address>```

## blocs
- [X] ```<div>```
- [X] ```<p>```
    - [X] Add support for title attribute
- [X] ```<hr>```
- [X] ```<pre>```
- [X] ```<blockquote>```
- [X] ```<ol>```
- [X] ```<ul>```
- [X] ```<li>```
- [X] ```<dl>```
- [X] ```<dt>```
- [X] ```<dd>```
- [X] ```<figure>```
- [X] ```<figcaption>```


## Tables
- [X] ```<table>```
- [X] ```<tbody>```
- [X] ```<thead>```
- [X] ```<tfoot>```
- [X] ```<tr>```
- [X] ```<td>```
- [X] ```<th>```
- [X] ```<caption>```
- [X] ```<colgroup>```
- [X] ```<col>```

## Textual semantic
- [X] ```<a>```
- [ ] ```<em>```
- [ ] ```<strong>```
- [ ] ```<small>```
- [ ] ```<s>```
- [ ] ```<cite>```
- [ ] ```<q>```
- [ ] ```<dfn>```
- [ ] ```<abbr>```
- [ ] ```<data>```
- [ ] ```<time>```
- [ ] ```<code>```
- [ ] ```<var>```
- [ ] ```<samp>```
- [ ] ```<kbd>```
- [ ] ```<sub>```
- [ ] ```<sup>```
- [ ] ```<i>```
- [ ] ```<b>```
- [ ] ```<u>```
- [ ] ```<mark>```
- [ ] ```<ruby>```
- [ ] ```<rt>```
- [ ] ```<bdi>```
- [ ] ```<bdo>```
- [ ] ```<span>```
- [ ] ```<br>```
- [ ] ```<wbr>```


## include sections
- [X] ```<img>```
- [ ] ~~```<iframe>```~~ 'Not supported in HTML 5'
- [ ] ```<object>```
- [ ] ```<param>```
- [ ] ```<video>```
- [ ] ```<audio>```
- [ ] ```<source>```
- [ ] ```<track>```
- [ ] ```<canvas>```
- [X] ```<map>```
- [X] ```<area>```
- [ ] ```<svg>```
- [ ] ```<math>```

## Forms
- [X] ```<form>```
- [ ] ```<fieldset>```
- [ ] ```<legend>```
- [ ] ```<label>```
- [X] ```<input>```
    - [ ] Add support for disabled attribut
- [ ] ```<button>```
- [ ] ```<select>```
- [ ] ```<datalist>```
- [ ] ```<optgroup>```
- [ ] ```<option>```
- [ ] ```<textarea>```
- [ ] ```<keyben>```
- [ ] ```<output>```
- [ ] ```<progress>```
- [ ] ```<meter>```

## Interactive data

- [ ] ```<detail>```
- [ ] ```<command>```
- [ ] ~~```<menu>```~~ --> This feature will not be implemented, since this feature is only avaible in Firefox.

## Function Design:


### Passing attributes

- [ ] Every HTML tag (PSHTML Function) should have 'at least' the following attributes available:
- Class
- ID
- Style

these are - in my opinion- the most commonly used attributes in HTML.

### Additional Attributes

- [ ] Each function has an additional parameter called: ```Attributes``` of type HashTable.
It will allow to add additional html tags without having to list ALL the existing attributes, and offer flexibility for custom and or special htmls attributes.