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

## App Technical Name Check
```text
 if (page.AppCode == "zides-unpaid-request-leave") {
  //write here
 }
```
## Show Error to User and Stop Executing Next Lines in Scheme
```text
try
{
  //in some conditions..
  throw Error("No Manager Assigned");


}
catch(error)
{
    page.Messages = page.Messages || [];
    page.Messages.push({
    Title: "Logical error",
    Text: ${error},
    IsError: true
  });
  return {
    continueNext: true,  //do it to show the message to user
    page,                        // page data
    container                    // container data
  };
}
```

## Read an Element and Assign Value
```text
  var _elemMANAGER = page.Elements.filter(o => o.FieldName === "MANAGER")[0];
  if (_elemMANAGER) {
    _elemMANAGER.Data = "Bekir Karadeniz";
  }
```
## Add Variable Value to Container
```text
  container.zhr_01 = "ZHR_01 executed in page load";
```
## Read Page Events
```text
  if (page.PageEvent == "Get") {
    //occurs when page loads
  }
  else if (page.PageEvent == "Post") {
    //occurs on button click
    if(page.PostFieldName =="DOSUBMIT")
    {
        container._Manager = "00000002";
        container._NewStatus = "SUBMITTED";
    }
  }
  else if (page.PageEvent == "PostBack") {
    //occurs on page element change
    if(page.PostBackField =="LAND1")
    {
      //LAND1 select has been changed
    }
  }
```
