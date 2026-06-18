# UI Components

This document summarizes the dynamically rendered UI components on the page and the core properties used by these components.

## Contents

- [HStack](#hstack)
- [VStack](#vstack)
- [Card](#card)
- [TabControl](#tabcontrol)
- [Header1](#header1)
- [Header2](#header2)
- [Header3](#header3)
- [Header4](#header4)
- [Text](#text)
- [Tile](#tile)
- [CardFigure](#cardfigure)
- [CardTable](#cardtable)
- [Img](#img)
- [TextBox](#textbox)
- [TextArea](#textarea)
- [File](#file)
- [Select](#select)
- [MultiSelect](#multiselect)
- [Table](#table)
- [View](#view)
- [CheckBox](#checkbox)
- [CheckList](#checklist)
- [Radio](#radio)
- [Button](#button)
- [LinkButton](#linkbutton)
- [DatePicker](#datepicker)
- [TimePicker (Time)](#timepicker-time)
- [DateIntervalPicker](#dateintervalpicker)
- [PeriodPicker](#periodpicker)
- [Rectangle](#rectangle)
- [Circle](#circle)
- [PieChart](#piechart)
- [DonutChart](#donutchart)
- [BarChart](#barchart)
- [LineChart](#linechart)
- [OrcTreeView (OrcObjTree)](#orctreeview-orcobjtree)
- [OrcPosSelect](#orcposselect)
- [OrcCalendar](#orccalendar)
- [OrcReportView](#orcreportview)
- [OrcProfile](#orcprofile)
- [OrcExInput](#orcexinput)

## Common Dynamic Behaviors

- All elements are selected and rendered by `element.FieldType` inside the `renderElement(element, renderDepth)` function.
- When `designMode` is enabled, select, drag-and-drop, delete, and sort operations become active.
- In runtime mode, many input elements update their own `Data` field through `handleElementPropertyUpdate` via `OnChange`/`OnSelect`.
- Some URL and text fields resolve dynamic values with `replacePlaceholders(...)`.

## HStack

- Component: `OrcHStack`
- Properties: `isDesignMode`, `onDelete`, `onMove`
- Child management: horizontal layout based on the `element.Children` list, with drag-and-drop support inside the container.

## VStack

- Component: `OrcVStack`
- Properties: `content`, `isDesignMode`, `onDelete`, `onMove`
- Child management: vertical layout, with drag-and-drop support inside the container.

## Card

- Component: `OrcCard`
- Properties: `Title`, `SubTitle`, `expanded`
- Child management: other elements can be dropped inside the card.

## TabControl

- Runtime structure: custom tab header + content container.
- Tabs are found using the `ParentId === element.Id` filter.
- Active tab index: `ActiveTabIndex`
- In the tab content area, tab-based components are rendered with `ParentId === activeTab.FieldName`.
- Header icon support: `tab.IconName`

## Header1
Shows top level heading text in the page.

- Component: `OrcHeader1`
- Properties: `FieldName`, `Caption`, `ClassName`, `ContainerClassName`

In programs, use Caption prop to update text.

## Header2
Shows second level heading text in the page.
- Component: `OrcHeader2`
- Properties: `FieldName`, `Caption`, `ClassName`, `ContainerClassName`

In programs, use Caption prop to update text.

## Header3
Shows third level heading text in the page.
- Component: `OrcHeader3`
- Properties: `FieldName`, `Caption`, `ClassName`, `ContainerClassName`

In programs, use Caption prop to update text.

## Header4
Shows fourth level heading text in the page.
- Component: `OrcHeader4`
- Properties: `FieldName`, `Caption`, `ClassName`, `ContainerClassName`

In programs, use Caption prop to update text.

## Text
Shows paragraph text in the page.
- Component: `OrcText`
- Properties: `FieldName`, `Caption`, `ClassName`, `ContainerClassName`

In programs, use Caption prop to update text.

## Tile

- Component: `OrcTile`
- Properties: `FieldName`, `Title`, `SubTitle`, `IconName`, `Figure`, `NavigateTo`, `OpenNewTab`
- Runtime: `NavigateTo` is generated dynamically via placeholder resolution.

## CardFigure

- Component: `OrcCardFigure`
- Properties: `FieldName`, `IconName`, `Title`, `SubTitle`, `Figure`

## CardTable

- Component: `OrcCardTable`
- Properties: `FieldName`, `Title`, `SubTitle`, `Rows`

## Img

- Runtime component: native `img`
- Properties: `src <- UrlSrc`, `alt <- Caption`, `Rounded`, `ClassName`
- Additional behavior: placeholder view for empty `UrlSrc`, plus zoom/lightbox image support.

## TextBox

- Component: `OrcTextBox`
- Core properties: `FieldName`, `Data`, `Caption`, `PlaceHolder`, `Type`, `DataType`, `Length`, `Regex`, `Required`, `Disabled`, `HasError`, `RollName`, `DomName`
- Events: `OnChange`, `HelpRequested`

## TextArea

- Component: `OrcTextArea`
- Core properties: `FieldName`, `Data`, `Caption`, `PlaceHolder`, `Rows`, `Required`, `Disabled`, `ClassName`, `ContainerClassName`, `CaptionClassName`
- Events: `OnChange`, `HelpRequested`

## File

- Component: `OrcFile`
- Core properties: `FieldName`, `Data`, `Caption`, `Required`, `Disabled`, `HasError`, class fields
- Event: `OnChange(file, uploadResult)`
- Data flow: instead of storing the file itself, `uploadResult.id` is written to the `Data` field.

## Select

- Component: `OrcSelect`
- Core properties: `FieldName`, `Data`, `Caption`, `Items`, `DataType`, `Required`, `Disabled`, `HasError`, `RollName`, `DomName`
- Events: `OnSelect`, `HelpRequested`

## MultiSelect

- Component: `OrcMultiSelect`
- Core properties: `FieldName`, `Data`, `Caption`, `Items`, `DataType`, `Required`, `Disabled`, `HasError`, `RollName`, `DomName`
- Events: `OnSelect`, `HelpRequested`

## Table

- Component: `OrcTable`
- Core properties: `FieldName`, `Data`, `Columns`, `ClassName`
- Command flags: `HasNewCommand`, `HasEditCommand`, `HasDetailCommand`, `HasRefreshCommand`, `HasDeleteCommand`, `HasCopyCommand`
- Other: `Scenerio`, `ShowVariant`, `DefaultVariant`
- Event: `CommandClicked`

## View

- Component: `OrcView`
- Properties: `FieldName`, `ClassName`, `ViewName`, `Otype`, `Objid`, `Plvar`, `Subty`, `Begda`, `Endda`

## CheckBox

- Component: `OrcCheckBox`
- Properties: `FieldName`, `Data`, `Caption`, `Required`, `Disabled`, `HasError`, class fields
- Events: `OnChecked`, `HelpRequested`

## CheckList

- Component: `OrcCheckList`
- Properties: `FieldName`, `Data`, `Caption`, `Items`, `DataType`, `Required`, `Disabled`, `HasError`, `RollName`, `DomName`
- Events: `OnSelect`, `HelpRequested`

## Radio

- Component: `OrcRadio`
- Properties: `FieldName`, `Data`, `Caption`, `Items`, `DataType`, `Required`, `Disabled`, `HasError`, `RollName`, `DomName`
- Events: `OnSelect`, `HelpRequested`

## Button

- Component: `OrcButton`
- Properties: `FieldName`, `Caption`, `NavigateTo`, `ClassName`
- Runtime event: `OnClicked -> handleElementClicked(...)`

## LinkButton

- Component: `LinkButton`
- Properties: `FieldName`, `Title`, `Url`, `SubTitle`, `IconName`, `ClassName`, `ContainerClassName`
- Runtime: the link is generated from `UrlSrc` via placeholder resolution.

## DatePicker

- Component: `OrcDatePicker`
- Properties: `FieldName`, `Data`, `Caption`, `Required`, `Disabled`, `HasError`, class fields
- Events: `OnChange`, `HelpRequested`

## TimePicker (Time)

- Component: `OrcTimePicker`
- Properties: `FieldName`, `Data`, `Caption`, `Required`, `Disabled`, `HasError`, class fields
- Events: `OnChange`, `HelpRequested`

## DateIntervalPicker

- Component: `OrcDateIntervalPicker`
- Properties: `FieldName`, `Data (YYYYMMDDYYYYMMDD)`, `Caption`, `Scenerio`, `Required`, `Disabled`, `HasError`, class fields
- Events: `OnChange`, `HelpRequested`

## PeriodPicker

- Component: `OrcPeriodPicker`
- Properties: `FieldName`, `Data`, `Caption`, `Required`, `Disabled`, `HasError`
- Events: `OnChange`, `HelpRequested`

## Rectangle

- Runtime: rectangular block using native `div`.
- Design mode: simple geometry preview with select/delete support.

## Circle

- Runtime: circular block using native `div`.
- Design mode: simple geometry preview with select/delete support.

## PieChart

- Component: `OrcPieChart`
- Properties: `Title`, `DataSourceUrl`, `Progname`, `Height`, `ClassName`

## DonutChart

- Component: `OrcDonutChart`
- Properties: `Title`, `DataSourceUrl`, `Progname`, `Height`, `ClassName`

## BarChart

- Component: `OrcBarChart`
- Properties: `Title`, `DataSourceUrl`, `Progname`, `Height`, `ClassName`

## LineChart

- Component: `OrcLineChart`
- Properties: `Title`, `DataSourceUrl`, `Progname`, `Height`, `ClassName`

## OrcTreeView (OrcObjTree)

- Component: `OrcObjTree`
- Properties: `FieldName`, `Scenario`, `Begda`, `Endda`, `Plvar`
- Note: values such as date/plvar are read with `getPropValue(element.Props, ...)`.

## OrcPosSelect

- Component: `OrcPosSelect`
- Properties: `value`, `placeholder`, `disabled`, `scenario`, `begda`, `endda`, `plvar`, `depth`, `className`
- Event: `onChange(selectedPosition)`

## OrcCalendar

- Component: `OrcCalendar`
- Properties: `Pernr`, `ClassName`, `Scenerio`
- Runtime note: the `Pernr` value is generated through placeholder resolution.

## OrcReportView

- Component: `OrcReportView`
- Properties: `title`, `className`, `maxHeight`, `showHeader`, `Nodes`
- Events: `onReportClick`, `onFolderClick`

## OrcProfile

- Component: `OrcProfile`
- Properties: `FieldName`, `ContainerClassName`, `Plvar`, `Otype`, `Objid`

## OrcExInput

- Component: `OrcExInput`
- Properties: `FieldName`, `ContainerClassName`, `Caption`, `PlaceHolder`, `Disabled`, `Required`, `Plvar`, `Otype`, `SearchHelp`, `Data`
- Event: `onSelect` (the selected value is written to the `Data` field)

