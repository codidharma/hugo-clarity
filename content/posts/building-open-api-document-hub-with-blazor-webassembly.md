---
title: Building Open API document Hub with Blazor Webassembly
date: 2022-09-25T17:43:01.646Z
draft: false
featured: true
toc: true
categories: '"Coding"'
tags: List ("Blazor, Open API")
---
In last few years I have worked on/ seen few projects where there were multiple REST APIS in the enterprise and there was  `Open API` (previously `Swagger`) documentation for each one of them. Creating documentation for your REST APIs is undoubtedly a best practice and every developer worth their salt should adopt this. In cases of microservices architecture, there are always multiple APIS that need to be called from the frontend for the product to deliver the value it should. In such cases having robust documentation always helps. But this creates another problem, as the number of services grow, it becomes a cumbersome task to go to the documentation of each of the service and read it. In such case having a swagger document hub where there is a collection of multiple API definition can definitely add value.

Today I will try to solve this problem by creating an \`Open API\` document using Blazor Web Assembly and SwaggerUI JavaScript. This document hub can be extended to house other important enterprise documentation which teams might need from time to time e.g., production deployment processes, general guidance on repository management etc.

For this demonstration, I am going to use following tech stack

1. Visual Studio 2019 (you can use VS code if you like)
2. .Net Core 5.0

## C﻿reating the Project

We can create the project using the BlazorWebAssembly template available in Visual Studio.

![](images/building-open-api-document-hub-with-blazor-webassembly/selectblazorprojecttemplate.png "Select Project Template")

![](images/building-open-api-document-hub-with-blazor-webassembly/projectname.png "Provide Project Name")

![](images/building-open-api-document-hub-with-blazor-webassembly/configureparameters.png "Configure Parameters")

Once done the project structure looks like following.

![](images/building-open-api-document-hub-with-blazor-webassembly/projectstructure.png "Project Structure")

## S﻿wagger UI JavaScripts

Now we need the javascripts to render the \`OpenAPI\` / \`Swagger\` documentation. Fortunately, we do not need to write these scripts ourselves, these scripts are released on GITHUB for us to use. We can get them from [swagger-api/swagger-ui](https://github.com/swagger-api/swagger-ui) repository. All we need to do is download the latest release from [Swagger UI Latest release](https://github.com/swagger-api/swagger-ui/releases/tag/v3.43.0)

O﻿nce this is done, we need to extract out the release and we need to get following three things from the `dist` folder.

1. s﻿wagger-ui-bundle.js
2. s﻿wagger-ui-standalone-preset.js
3. s﻿wagger-ui.css

W﻿e can put the javascript files in a seperate folder for javascript in wwwroot folder

![](images/building-open-api-document-hub-with-blazor-webassembly/javascriptsadded.png "Javascripts Added")

N﻿ext step is to refer these scripts in the `index.html` page of the blazor application. It can be easily done as follows.

```html
<script src="javascripts/swagger-ui-bundle.js"></script>
<script src="javascripts/swagger-ui-standalone-preset.js"></script>
```

Before we configure the function to render the swagger ui, we need to add few API documentations links in our project (As we will see later, we can directly refer to the documentation using their urls).

I﻿ have taken two documents for this demo. I have added

1. P﻿etstorev3.json file which is the `OpenAPI` version
2. P﻿ertstorev2.json file which is the `Swagger` version

N﻿ext we add the javascript to load the swagger UI in our `index.html` page. We can also create a new javascript file for this and then refer it in the `index.html`. The javascript looks like following

```html
<script>
        window.buildSwaggerUI = function (domId) {
            // Begin Swagger UI call region
            const ui = SwaggerUIBundle({
                urls: [
                    {
                        url: "apidefinitions/PetStorev2.json",
                        name: "PetStore V2"
                    },
                    {
                        url: "apidefinitions/PetStorev3.json",
                        name: "PetStore V3"
                    }
                ],
                dom_id: domId,
                deepLinking: true,
                presets: [
                    SwaggerUIBundle.presets.apis,
                    SwaggerUIStandalonePreset
                ],
                plugins: [
                    SwaggerUIBundle.plugins.DownloadUrl
                ],
                layout: "StandaloneLayout"
            })
            
            window.ui = ui
        }
    </script>
```