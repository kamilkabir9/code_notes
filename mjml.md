# [mjml](https://mjml.io/)

# Install
```bash
npm install mjml
```
# Create html from mjml
Transpile mjml to html.
```bash
mjml index.mjml -o index.html
```

# Live edit mjml and watch html
Transpile mjml to html in real time.
you can edit mjml and open html to see edits in real time.
```bash
mjml --watch index.mjml index.html
```

---

# Include mjml in inside mjml
mj-include adds mjml inside mjml 
```html
<mj-include path="./EmailSignature.mjml" />
```


# Sample file
Following sample uses [StringTemplate](https://stringtemplate.org) for string templating ([ref](https://github.com/kamilkabir9/code_notes/blob/master/csharp.md#string-templates-using-stringtemplate)).
```html
<mjml>
  <mj-head>
    <mj-preview>Alert Email</mj-preview>
    <mj-title>Alert Email</mj-title>
  </mj-head>
  <mj-body background-color="#B8CCE2">
    <mj-section background-color="#8abfc0">
      <mj-column width="100%">        
        <mj-text font-weight=bolder font-size=25px>
          Company Name
        </mj-text>
      </mj-column>
    </mj-section>
    <mj-section background-color="#FFFFFF">
      <mj-column>
        <mj-text><span> Dear <span style="font-weight:bolder;font-size:14px;">$CostControllerName$,</span></span>
        </mj-text>
        <mj-text>
          You have $requestCount$ requests pending.
        </mj-text>
      </mj-column>
    </mj-section>
    <mj-section background-color="#FFFFFF">
      <mj-column width="55%">        
      </mj-column>
    </mj-section>
    <mj-section background-color="#eeeef5">
      <mj-column width="40%">
        <mj-button border-radius="25px" background-color="#FFD500" color="black" font-weight="bold" href="$SubprojectEntryPageURL$">
          TAKE ACTION >
        </mj-button>
      </mj-column>
      <mj-column width="60%">
      </mj-column>
    </mj-section>
    <mj-include path="./EmailSignature.mjml" />
  </mj-body>
</mjml>
```

# Online demos
1. https://mjml.io/try-it-live
2. https://grapesjs.com/demo-mjml.html

