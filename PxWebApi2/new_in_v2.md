# What's new in PxWebApi version 2

I have previously worked with SSB's external APIs. I do some work on it in my spare time as a retiree. 
Here is a brief overview of what is new in the new version of PxWebApi v2.

**Most important** is the GET URL support. This makes the API easier to integrate.

**Better masking characters**. You can now mask individual characters with ? , in addition to the existing * for multiple characters. E.g.: 202?M12 - only December numbers for the 2020s.

**New filters**
- from() – retrieve data from and including a starting point
- range([,]) – define a specific interval, e.g. from municipal list
- to() - up to and including (inclusive)
- bottom() - opposite of the existing top()

**More flexible data retrieval**
- Fetch predefined datasets without specifying parameters
- More metadata in JSON-stat2, including footnotes (also applies to version 1)
- Metadata in the API is can now be shown as JSON-stat2
- Codelists in metadata. Available via Codelists 

**Layout control for CSV and XLSX**
New parameters give full control and flexibility over what goes in rows and columns. This makes CSV and XLSX much more usable.

**View options**
- UseCodes – show only codes
- UseTexts – show only text
- UseCodesAndTexts – show both codes and text
- IncludeTitle – include table title

**Structure control**
- stub – determine which variables should be displayed in the front column
- heading – determine variables in the table header
**Tip**: Place all variables in stub to get a pivot-friendly table

**CSV separator**
- SeparatorTab – tabulator
- SeparatorSpace – space
- SeparatorSemicolon – semicolon
- 
Example:
```
    outputformat=csv
    outputformatparams=separatorsemicolon,usecodesandtexts
    heading=ContentsCode
    stub=VareGrupper2,Tid
```

**HTML output** is new in the API.
Styling tips:
```
	<style type="text/css">
	    th[scope="col"] {
	        text-align: center;
	    }
	    th[scope="row"] {
	        text-align: left;
	    }
	    td {
	        text-align: right;
	    }
	    caption{
	    	font-weight: bold;
	    }
	</style>
```

**Other news**
More metadata in JSON-stat 2, such as footnotes. Also applies to API v1

There is also a new format for using http POST.
Its also good to know that it seems like the  GET URL is not case sensitive.

**Known limitations**
**Static URLs**
URLs generated in Statbank are static and do not automatically include future figures. It is necessary to edit it manually to get updated figures. Use e.g. filter from() or top(). In version 1 it was possible to "select all" by eliminating the time variable. This is not possible in v2.  

**URL length limitation**
Maximum length of URL: ~2000 characters. This can be problematic for short-term statistics with long time series. For monthly statistics, the limit goes a little before the Financial Crisis, if they are not corrected.

---

Statistics Norways R-package [PxWebApiData](https://cran.r-project.org/package=PxWebApiData) is also updated to handle V2 URLs

See also [Statistics Norway's user guide for the new API](https://www.ssb.no/en/api/statbank-pxwebapi-user-guide).