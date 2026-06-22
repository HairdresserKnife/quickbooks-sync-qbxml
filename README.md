<div align="center">

# QuickBooks Sync

Utilities and NuGet packages for syncing data with QuickBooks Desktop using QBXML and the QuickBooks Web Connector.

</div>

## 📌 Overview

QuickBooks Sync provides .NET packages for working with QuickBooks Desktop data through QBXML. It includes tools for creating QuickBooks requests, parsing responses, and implementing Web Connector workflows for server-based synchronization.

The project is intended for applications that need to communicate with QuickBooks Desktop, exchange QBXML messages, process returned accounting data, and coordinate sync sessions through the QuickBooks Web Connector.

The original project notes that it is actively maintained and in an early alpha stage, so breaking changes may occur before stability is reached.

## 📦 Packages

| Package | Purpose |
| --- | --- |
| `QbSync.QbXml` | Build QBXML requests and parse QBXML responses from QuickBooks Desktop. |
| `QbSync.WebConnector` | Core abstractions for QuickBooks Web Connector workflows. |
| `QbSync.WebConnector.AspNetCore` | ASP.NET Core integration, including SoapCore/AspNetCore references. |

## 🧾 QBXML Support

QBXML is the XML-based language used by QuickBooks Desktop to exchange data with external applications.

`QbSync.QbXml` supports common QBXML workflows such as:

- Creating request XML for QuickBooks Desktop.
- Adding query filters such as maximum returned records or modified dates.
- Reading QuickBooks response XML.
- Mapping response data to typed response objects.
- Working with request and response pairs from the QuickBooks SDK.

Typical usage involves creating a request object, adding one or more QuickBooks request types, sending the generated XML to QuickBooks, and parsing the returned XML into response types.

## 🔌 Web Connector Support

QuickBooks Sync includes Web Connector support for applications that communicate with QuickBooks Desktop through Intuit’s QuickBooks Web Connector.

The Web Connector calls a server endpoint periodically and asks whether there are requests to process against the QuickBooks company file. Because the SOAP communication does not maintain long-lived state, applications using this workflow need to store session state, including tickets and current sync steps.

The Web Connector package abstracts much of the SOAP communication so application code can focus on authentication, sync steps, responses, and session state.

## ⚙️ General Installation

The original project documents the following NuGet packages:

- `QbSync.QbXml`
- `QbSync.WebConnector`
- `QbSync.WebConnector.AspNetCore`

Use the QBXML package when building and parsing QuickBooks XML messages. Use the Web Connector packages when implementing a server workflow for QuickBooks Desktop synchronization.

## 🧩 Main Concepts

### Authentication

Applications provide an authenticator that verifies a login/password combination, retrieves existing session tickets, and saves ticket state.

The authentication model includes:

- A session ticket exchanged with the Web Connector.
- A current step value used to track sync progress.
- An authenticated flag indicating whether the session is valid.

### Sync Steps

A sync workflow is organized into ordered steps. Each step can generate a QuickBooks request and process the matching QuickBooks response.

The original documentation describes step classes for tasks such as querying customers, handling invoices, or combining multiple QuickBooks requests into one grouped step.

### Iterators

Some QuickBooks queries can return many records. For larger result sets, iterator-based steps can be used to split work into batches. Iterator state must be saved and restored using the current ticket, step, and key.

By default, iterator-based queries are described as batching results in groups of 100 objects.

### Multiple Requests

For workflows that need to send more than one request at a time, the Web Connector support includes grouped request and response base classes. This allows several QuickBooks operations to be sent together while still processing their response types individually.

### Runtime Step Control

The step sequence can be changed at runtime by implementing methods that indicate either a specific next step or whether the workflow should continue to the next registered step.

## 🕒 Timestamp Handling

QuickBooks Desktop has known daylight saving time behavior that can affect timestamp offsets. The original project includes handling in the `DATETIMETYPE` class for common QuickBooks DST cases.

The documented approach is to work with corrected local date/time values when storing and reusing modification timestamps. The original documentation also describes access to raw QuickBooks timestamp strings and uncorrected date values for cases where an application must persist offset-based values.

## 🧱 Web Connector Workflow

The documented Web Connector flow includes these stages:

1. Authenticate the user and return a session ticket.
2. Send request XML to QuickBooks Desktop.
3. Receive response XML from QuickBooks Desktop.
4. Continue sending requests until no more work remains.
5. Close the connection.

Applications using this flow are expected to maintain sync state between calls, usually in a database or other persistent store.

## 🛠️ Extension Points

QuickBooks Sync documents several extension points for applications that need custom behavior:

- `IAuthenticator` for login, ticket retrieval, and ticket persistence.
- `IMessageValidator` for validating Web Connector versions and related request details.
- `IWebConnectorHandler` for processing client information, exceptions, wait times, company file selection, and connection closure.
- `QbManager` overrides for lower-level Web Connector communication behavior.

Most applications are expected to use the higher-level Web Connector handler rather than overriding lower-level manager behavior.

## 👥 Intended Use Cases

QuickBooks Sync is relevant for .NET applications that need to:

- Synchronize customer, invoice, or other accounting data with QuickBooks Desktop.
- Generate QBXML requests programmatically.
- Parse QuickBooks Desktop QBXML responses.
- Build ASP.NET Core endpoints for the QuickBooks Web Connector.
- Maintain server-side state for Web Connector sessions.
- Process sync workflows as ordered request/response steps.
- Handle large QuickBooks query results using iterator-based batching.

## ⚠️ Project Status

The original README describes the project as early alpha and actively maintained. It also warns that breaking changes may be introduced before the project reaches stability.

Use this project with that status in mind, especially for production systems that require stable APIs.

## ❓ FAQ

### What is QuickBooks Sync used for?

QuickBooks Sync is used to build .NET applications that communicate with QuickBooks Desktop through QBXML and, when needed, the QuickBooks Web Connector.

### Does it work with QuickBooks Desktop?

Yes. The project is specifically described as syncing data from QuickBooks Desktop using QBXML.

### What is QBXML?

QBXML is the XML format used by QuickBooks Desktop to exchange data with external applications.

### What does the Web Connector package do?

It helps implement the server-side workflow required by the QuickBooks Web Connector, including authentication, request generation, response handling, and sync step coordination.

### Does the project handle large query results?

The original documentation describes iterator-based query support for batching large QuickBooks result sets.

### Is this project stable?

The original README states that the project is in an early alpha stage and that breaking changes may occur before stability is reached.

## 📄 License

QuickBooks Sync is licensed under the MIT license.
