# Common TypeScript Codes


## Simple Validation Sample Code

```text
// page: IPageDesign
// container: IContainer

function run(page: IPageDesign, container: IContainer) {
  const pernr = container.UserPernr;
  const lang = page.Langu;

  page.Messages = page.Messages || [];

  if (page.PageEvent == "Get") {
    //page loading
    container.zhr_01 = "ZHR_01 executed in page load";
  }
  else if (page.PageEvent == "Post") {
    //page posted by button
    if (page.PostFieldName == "DOSUBMIT") {
      var _elem = page.Elements.filter(o => o.FieldName == "PHOTOFILE")[0];
      //
      if (_elem == null) {
        page.Messages.push({
          Title: "Error",
          Text: `PHOTOFILE not found in form`,
          IsError: true
        });
        return {
          continueNext: false,          // opsiyonel (yoksa true kabul ediliyor)
          page,                        // page data
          container                    // container data
        };
      }
      else {
        if (pernr == '00000002') {
          page.Messages.push({
            Title: "Error",
            Text: `Employee 00000002 cannot post this form ` + _elem.Data,
            IsError: true
          });
          return {
            continueNext: false,         // if false, stops scheme execution
            page,                        // page data
            container                    // container data
          };
        }

      }
    }
  }


  return {
    continueNext: true,          
    page,                        // page data
    container                    // container data
  };
} 
```

