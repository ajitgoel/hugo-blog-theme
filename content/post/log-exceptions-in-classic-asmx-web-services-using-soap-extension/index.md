---
title: Log exceptions in classic(asmx) web services using Soap Extension
date: 2022-03-23T03:26:53.164Z
draft: false
featured: false
tags:
  - Soap-Extension
  - Web-Services
  - ASP.Net
  - C#
  - Elmah
categories:
  - Soap-Extension
  - Web-Services
  - ASP.Net
  - C#
  - Elmah
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Problem:**\
Log exceptions in classic(asmx) webservices without adding try-catch blocks throughout all the webservice.

**Solution:**\
Exceptions can be easily logged in classic(asmx) webservices by using a Soap Extension.\
a. Create the soap extension. Override the call to **`ChainStream`** method where we get a new copy of the stream. Use the **`ProcessMessage`** switch to examine the Soap Envelope and log an exception using a logging framework of our choice(I have used `Elmah `logging framework here).\
b. Register the `SoapExtension `in the client application’s web configuration. The “type” property follows the format of\
`NameSpaceName.ClassName, SoapExtension’sAssemblyName`.

```
namespace SoapExtensions
{
    using System;
    using System.IO;
    using System.Web.Services.Protocols;
 
    using Elmah;
 
    public class ElmahSoapExtension : SoapExtension
    {
        private Stream oldStream;
        private Stream newStream;
 
        public string Request { get; private set; }
        public string Response { get; private set; }
 
        public override Stream ChainStream(Stream stream)
        {
            oldStream = stream;
            newStream = new MemoryStream();
            return newStream;
        }
 
        public override void ProcessMessage(SoapMessage soapMessage)
        {
            switch (soapMessage.Stage)
            {
                case SoapMessageStage.BeforeSerialize:
                    break;
                case SoapMessageStage.AfterSerialize:
                    Response = GetSoapEnvelope(newStream);
                    if (soapMessage.Exception != null)
                    {
                        var exception = soapMessage.Exception.GetBaseException();
                        exception.Data.Add("WebService Response", Response);
                        ErrorSignal.FromCurrentContext().Raise(exception);
                    }
                    CopyStream(newStream, oldStream);
                    break;
                case SoapMessageStage.BeforeDeserialize:
                    CopyStream(oldStream, newStream);
                    Request = GetSoapEnvelope(newStream);
                    break;
                case SoapMessageStage.AfterDeserialize:
                    break;
            }
        }
 
        private static string GetSoapEnvelope(Stream stream)
        {
            stream.Position = 0;
            var streamReader = new StreamReader(stream);
            var text = streamReader.ReadToEnd();
            stream.Position = 0;
            return text;
        }
 
        private static void CopyStream(Stream streamFrom, Stream streamTo)
        {
            var textReader = new StreamReader(streamFrom);
            var textWriter = new StreamWriter(streamTo);
            textWriter.WriteLine(textReader.ReadToEnd());
            textWriter.Flush();
        }
        public override object GetInitializer(LogicalMethodInfo methodInfo, SoapExtensionAttribute attribute)
        {
            return null;
        }
        public override object GetInitializer(Type WebServiceType)
        {
            return null;
        }
        public override void Initialize(object initializer)
        {
        }
    }
}
```

```
<?xml version="1.0"?>
<configuration>
	<system.web>
		<webServices>
      <soapExtensionTypes>
        <add 
        type="SoapExtensions.ElmahSoapExtension, SoapExtensions" 
        priority="1" group="1" />
      </soapExtensionTypes>      
		</webServices>
	</system.web>
</configuration>
```