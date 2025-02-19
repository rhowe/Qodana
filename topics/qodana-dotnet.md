[//]: # (title: Qodana for .NET)

[![official project](https://jb.gg/badges/official-flat-square.svg)](https://confluence.jetbrains.com/display/ALL/JetBrains+on+GitHub)

<img src="dotnet-linter.png" dark-src="dotnet-linter_dark.png" alt="Qodana for .NET linter languages" width="296"/>

<var name="linter" value="Qodana for .NET"/>
<var name="ide" value="Rider"/>
<var name="docker-image" value="jetbrains/qodana-dotnet:2023.3"/>
<var name="config-file" value="qodana-dotnet-docker-readme.xml"/>

%linter% is based on [%ide%](https://www.jetbrains.com/rider/) and provides static analysis for .NET projects.
It brings all the smarts from Rider, which help you:

* Detect anomalous code and probable bugs
* Eliminate dead code
* Highlight spelling problems
* Improve overall code structure
* Introduce coding best practices
* Upload inspection results to [Qodana Cloud](cloud-about.xml)

%linter% provides inspections for the C, C++, C#, VB.NET, JavaScript, and TypeScript programming languages.
C and C++ inspections of %linter% are limited by projects containing `.sln` solution files or `.csproj` project files.

Starting from version 2023.3, the functionality of .NET Framework-based project analysis is available in the 
[native mode](native-mode.md) of %product%.

<note>This linter requires the Qodana Cloud <a href="project-token.md">project token</a>.</note>

## Supported technologies

%linter% provides inspections for the following technologies.

<table header-style="none">
    <tr>
        <td>Programming languages</td>
        <td>
            <p>C#</p>
            <p>C</p>
            <p>C++</p>
            <p>JavaScript</p>
            <p>TypeScript</p>
            <p>VB.NET</p>
        </td>
    </tr>
    <tr>
        <td>Markup languages</td>
        <td>
            <p>CSS</p>
            <p>HTML</p>
            <p>JSON and JSON5</p>
            <p>RELAX NG</p>
            <p>T4</p>
            <p>XML</p>
            <p>XPath</p>
            <p>XSLT</p>
            <p>YAML</p>
        </td>
    </tr>
    <tr>
        <td>Scripting languages</td>
        <td>
            <p>Shell script</p>
        </td>
    </tr>
        <tr>
        <td>Databases and ORM</td>
        <td>
            <p>MongoJS</p>
            <p>MySQL</p>
            <p>Oracle</p>
            <p>PostgreSQL</p>
            <p>SQL</p>
            <p>SQL server</p>
        </td>
    </tr>
    <tr>
        <td>Build management</td>
        <td>
            <p>MSBuild</p>
        </td>
    </tr>
    <tr>
        <td>Frameworks and libraries</td>
        <td>
            <p>.NET Framework</p>
            <p>.NET Core</p>
            <p>Handlebars/Mustache</p>
            <p>Less</p>
            <p>Node.JS</p>
            <p>NUnit</p>
            <p>Pug/Jade</p>
            <p>Sass/SCSS</p>
            <p>Unity</p>
            <p>Unreal Engine</p>
            <p>Vue</p>
            <p>Xunit</p>
        </td>
    </tr>
</table>

Here, C and C++ inspections are applicable for projects containing `.sln` files.

## Supported features

<include src="lib_qd.xml" include-id="linters-supported-features" use-filter="empty,dotnet"/>

## Analyze a project locally

### Install project dependencies (non-native mode)

%linter% is suitable for analyzing .NET projects, and the Dockerized version of this linter provides the following SDK versions:

* 6.0.417
* 7.0.404
* 8.0.100

All SDK versions are stored in the `/usr/share/dotnet/sdk` directory of the %linter% container filesystem.

In case a project requires a different version of the SDK, you can set it up before running the analysis using the
[`bootstrap`](before-running-qodana.md) field in the `qodana.yaml` file.
For example, this command will install the required version of the SDK that is specified in the
`global.json` file and located in the root of your project:

<code style="block" lang="yaml">
    bootstrap: curl -fsSL https://dot.net/v1/dotnet-install.sh |
      bash -s -- --jsonfile /data/project/global.json -i /usr/share/dotnet
</code>

### Build the project

Build the project before inspecting it using %product%. You can do it by using the [`bootstrap`](before-running-qodana.md)
option of the [`qodana.yaml`](qodana-yaml.md) file contained in your project directory.

### Configure %product%

Starting from version 2023.3 of %product%, the native mode is the recommended method for running the %linter% linter. 
We recommend running the native mode on the same machine where you build a project because this can guarantee
that %product% has access to private NuGet feeds.

#### Specify a solution

<p><include src="lib_qd.xml" include-id="docker-dotnet-specific-solution-project" use-filter="empty,for-dotnet"/></p>

#### Roslyn analyzers

.NET projects have Roslyn analyzers as separate inspections, and you can configure them using the
<code>EditorConfig</code> files. To disable them, you can [configure the %product% profile](custom-profiles.md) using 
the `qodana.yaml` file, for example: 

```yaml
name: "Custom profile"

baseProfile: qodana.starter

groups: # List of configured groups
  - groupId: InspectionsToExclude
    groups:
      - "category:C#/Roslyn Analyzers"

inspections: # Group invocation
  - group: InspectionsToExclude
    enabled: false # Disable the InspectionsToExclude group
```

Another configuration example is available [on GitHub](https://github.com/hybloid/fluentassertions/blob/develop/qodana.yaml).

### Run Qodana

<p>You can run <a href="https://github.com/JetBrains/qodana-cli">Qodana CLI</a> in the native mode, which is the recommended method 
for the %linter% linter. Alternatively, you can use the Docker command from the <menupath>Docker image</menupath> tab.</p>

<tabs>
    <tab id="qodana-dotnet-cli-tab" title="Native mode using Qodana CLI">
        <p>Assuming that you have already
            <a href="https://github.com/JetBrains/qodana-cli/releases/latest">installed</a> Qodana CLI on your
            machine and followed the recommendations from 
<a href="native-mode.md" anchor="Before+you+start">this section</a>, you can run this command in the project root directory:</p>
        <code style="block" lang="shell" prompt="$">
            qodana scan \
            &nbsp;&nbsp;&nbsp;--ide QDNET
        </code>
        <p>Here, the <code>--ide</code> option downloads and employs the JetBrains IDE binary file.</p>
        <p>Alternatively, in the <code>qodana.yaml</code> file you can save the <code>ide: QDNET</code> configuration, 
    and then run %product% using this command:</p>
        <code style="block" lang="shell" prompt="$">
            qodana scan
        </code>
    </tab>
    <tab id="qodana-dotnet-docker-image-tab" title="Docker image">
        <p>To start, pull the image from Docker Hub (only necessary to get the latest version):</p>
        <code filter="non-gs" style="block" lang="shell" prompt="$">
            docker pull %docker-image%
        </code>
        <p>Start local analysis with <code>source-directory</code>
            pointing to the root of your project and
            <code>QODANA_TOKEN</code> referring to the <a href="project-token.md">project token</a>:</p>
        <code style="block" lang="shell" prompt="$">
        docker run \
        &nbsp;&nbsp;&nbsp;-v &lt;source-directory&gt;/:/data/project/ \
        &nbsp;&nbsp;&nbsp;-e QODANA_TOKEN="&lt;cloud-project-token&gt;" \
        &nbsp;&nbsp;&nbsp;-p 8080:8080 \
        &nbsp;&nbsp;&nbsp;%docker-image% \
        &nbsp;&nbsp;&nbsp;--show-report
        </code>
        <p>Open <code>http://localhost:8080</code> in your browser to examine inspection results.
            Here, you can also reconfigure the analysis, see the <a href="ui-overview.md"/> section for
            details. When done, you can stop the web server by pressing Ctrl-C in the
            console.</p>
    </tab>
</tabs>

## Next steps

<include src="lib_qd.xml" include-id="linter-next-steps-footer" use-filter="empty"/>