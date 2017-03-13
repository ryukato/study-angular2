# Lint for Frontend
## TSLint
TSLint is a linter for Typescript language.

## Rules
### Support Rules
* [Typescript specific rules](https://palantir.github.io/tslint/rules/)
### Rule file
* tslint.json

## Formatters
#### checkstyle (xml format)
##### sample output

```
<?xml version="1.0" encoding="utf-8"?>
	<checkstyle version="4.3">
		<file name="src/app/app.component.ts">
			<error line="10" column="47" severity="warning" message="Missing semicolon" source="failure.tslint.semicolon" />
		</file>
	</checkstyle>
```
#### codeFrame
##### sample output

```
Parentheses are required around the parameters of an arrow function definition (arrow-parens)
  21 |     public componentDidMount() {
  22 |         this.input.focus();
> 23 |         loadStripe().then(Stripe => Stripe.pay());
     |                          ^
  24 |     }
  25 |
  26 |     public render() {
```

#### fileList
##### sample output

```
directory/myFile.ts
```

#### json
##### sample output

```
[
    {
        "endPosition": {
            "character": 13,
            "line": 0,
            "position": 13
        },
        "failure": "Missing semicolon",
        "fix": {
            "innerRuleName": "semicolon",
            "innerReplacements": [
                {
                    "innerStart": 13,
                    "innerLength": 0,
                    "innerText": ";"
                }
            ]
        },
        "name": "myFile.ts",
        "ruleName": "semicolon",
        "startPosition": {
            "character": 13,
            "line": 0,
            "position": 13
        }
    }
]
```

#### msbuild
##### sample output

```
myFile.ts(1,14): warning: Missing semicolon
```

#### PMD
##### sample output
```
<pmd version="tslint">
    <file name="myFile.ts">
        <violation begincolumn="14" beginline="1" priority="1" rule="Missing semicolon"></violation>
    </file>
</pmd>
```

#### prose
##### sample output
```
myFile.ts[1, 14]: Missing semicolon
```
#### stylish
##### sample output
```
myFile.ts
1:14  semicolon  Missing semicolon
```

#### verbose
##### sample output
```
(semicolon) myFile.ts[1, 14]: Missing semicolon
```

#### vso
##### sample output
```
##vso[task.logissue type=warning;sourcepath=myFile.ts;linenumber=1;columnnumber=14;code=semicolon;]Missing semicolon
```

#### custom
##### custom formatter
```
import { AbstractFormatter } from 'tslint/lib/language/formatter/abstractFormatter';
import { IFormatterMetadata } from 'tslint/lib/language/formatter/formatter';
import { RuleFailure } from 'tslint/lib/language/rule/rule';

export class Formatter extends AbstractFormatter {
  FAILURE_HTML_TEMPLATE = `
  <div class="">
    <h3>filename</h3>
    <p>{{fileName}}</p>
    <h3>ruleName</h3>
    <p>{{ruleName}}</p>
    <h3>start position</h3>
    <p>{{startPosition}}</p>
    <h3>end position</h3>
    <p>{{endPosition}}</p>
    <h3>failure</h3>
    <p>{{failure}}</p>
    <h3>fix</h3>
    <h4>innerRuleName</h4>
    <p>{{innerRuleName}}</p>
    <h4>innerReplacements</h4>
    <p>{{innerReplacements}}</p>
  </div>
  `;
  public format(failures: RuleFailure[]): string {
    return failures.map((failure: RuleFailure) => this.wrapHtml(failure)).toString();
  }

  public wrapHtml(failure: RuleFailure): string {
    var content = this.FAILURE_HTML_TEMPLATE;
    content = content.replace("{{fileName}}", failure["fileName"])
              .replace("{{ruleName}}", failure["ruleName"])
              .replace("{{startPosition}}", JSON.stringify(failure["startPosition"]))
              .replace("{{endPosition}}", JSON.stringify(failure["endPosition"]))
              .replace("{{failure}}", failure["failure"])
              .replace("{{innerRuleName}}", failure["fix"]["innerRuleName"])
              .replace("{{innerReplacements}}", JSON.stringify(failure["fix"]["innerReplacements"].map((e:any) => JSON.stringify(e))).replace(/\\/g, ""))
              ;
    return content;
  }
}
```

##### run command

```
node_modules/tslint/bin/tslint -c tslint.json -s ./lint/formatter/ -o lint_result.html -t custom  ./src/*.ts, ./src/**/*.ts
```

## With IntelliJ


## With Atom

## Run

```
node_modules/tslint/bin/tslint -c tslint.json ./src/*.ts, ./src/**/*.ts
```

### Options

```
Options:
  -c, --config          configuration file
  -e, --exclude         exclude globs from path expansion
  --fix                 fixes linting errors for select rules (this may overwrite linted files)
  --force               return status code 0 even if there are lint errors
  -h, --help            display detailed help
  -i, --init            generate a tslint.json config file in the current working directory
  -o, --out             output file
  --project             tsconfig.json file
  -r, --rules-dir       rules directory
  -s, --formatters-dir  formatters directory
  -t, --format          output format (prose, json, stylish, verbose, pmd, msbuild, checkstyle, vso, fileslist, codeFrame)  [default: "prose"]
  --test                test that tslint produces the correct output for the specified directory
  --type-check          enable type checking when linting a project
  -v, --version         current version
```

