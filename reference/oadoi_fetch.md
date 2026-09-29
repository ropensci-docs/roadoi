# Fetch open access status information and full-text links using Unpaywall

This is the main function to retrieve comprehensive open access status
information from Unpaywall data service. Please play nice with the API.
For each user, 100k calls per day are suggested. If you need to access
more data, there is also a data dump available. For more info see
<https://unpaywall.org/products/snapshot>.

## Usage

``` r
oadoi_fetch(
  dois = NULL,
  email = Sys.getenv("roadoi_email"),
  .progress = "none",
  .flatten = FALSE
)
```

## Arguments

- dois:

  character vector, search by a single DOI or many DOIs. A rate limit of
  100k requests per day is suggested. If you need to access more data,
  request the data dump <https://unpaywall.org/dataset> instead.

- email:

  character vector, mandatory! Unpaywall requires your email address, so
  that they can track usage and notify you when something breaks. Set
  email address in your \`.Renviron\` file with the option
  \`roadoi_email\` `options(roadoi_email = "najko.jahn@gmail.com")`. You
  can open your \`.Renviron\` file calling \`file.edit("~/.Renviron")\`.
  Save the file and restart your R session. To stop sharing your email
  when using rcrossref, delete it from your \`.Renviron\` file.

- .progress:

  Shows the `plyr`-style progress bar. Options are "none", "text", "tk",
  "win", and "time". See
  [`create_progress_bar`](https://rdrr.io/pkg/plyr/man/create_progress_bar.html)
  for details of each. By default, no progress bar is displayed.

- .flatten:

  Simplify open access evidence output. If \`TRUE\` it transforms the
  nested column oa_locations so that each open access evidence variable
  has its own column and each row represents a single full-text.
  Following these basic principles of "Tidy Data" makes data analysis
  and export as a spreadsheet more straightforward.

## Value

The result is a tibble with each row representing a publication. Here
are the returned columns and descriptions according to the API docu:

|  |  |
|----|----|
| `doi` | DOI (always in lowercase). |
| `best_oa_location` | list-column describing the best OA location. Algorithm prioritizes publisher hosted content (eg Hybrid or Gold), then prioritizes versions closer to the version of record (PublishedVersion over AcceptedVersion), then more authoritative repositories (PubMed Central over CiteSeerX). |
| `oa_locations` | list-column of all the OA locations. |
| `oa_locations_embargoed` | list-column of locations expected to be available in the future based on information like license metadata and journals' delayed OA policies |
| `data_standard` | Indicates the data collection approaches used for this resource. `1` mostly uses Crossref for hybrid detection. `2` uses a more comprehensive hybrid detection methods. |
| `is_oa` | Is there an OA copy (logical)? |
| `is_paratext` | Is the item an ancillary part of a journal, like a table of contents? See here for more information <https://support.unpaywall.org/support/solutions/articles/44001894783>. |
| `genre` | Publication type |
| `oa_status` | Classifies OA resources by location and license terms as one of: gold, hybrid, bronze, green or closed. See here for more information <https://support.unpaywall.org/support/solutions/articles/44001777288-what-do-the-types-of-oa-status-green-gold-hybrid-and-bronze-mean->. |
| `has_repository_copy` | Is a full-text available in a repository? |
| `journal_is_oa` | Is the article published in a fully OA journal? |
| `journal_is_in_doaj` | Is the journal listed in the Directory of Open Access Journals (DOAJ). |
| `journal_issns` | ISSNs, i.e. unique numbers to identify journals. |
| `journal_issn_l` | Linking ISSN. |
| `journal_name` | Journal title, not normalized. |
| `publisher` | Publisher, not normalized. |
| `published_date` | Date published |
| `year` | Year published. |
| `title` | Publication title. |
| `updated_resource` | Time when the data for this resource was last updated. |
| `authors` | Lists author information (`family` name, `given` name and author role `sequence`), if available. |

The columns `best_oa_location`. `oa_locations` and
`oa_locations_embargoed` are list-columns that contain useful metadata
about the OA sources found by Unpaywall.

If `.flatten = TRUE` the list-column `oa_locations` will be restructured
in a long format where each OA fulltext is represented by one row.

These are:

|  |  |
|----|----|
| `endpoint_id` | Unique repository identifier. |
| `evidence` | How the OA location was found and is characterized by Unpaywall? |
| `host_type` | OA full-text provided by `publisher` or `repository`. |
| `is_best` | Is this location the `best_oa_location` for its resource? |
| `license` | The license under which this copy is published, e.g. Creative Commons license. |
| `pmh_id` | OAI-PMH endpoint where we found this location. |
| `repository institution` | Hosting institution of the repository. |
| `updated` | Time when the data for this location was last updated. |
| `url` | The `url_for_pdf` if there is one; otherwise landing page URL. |
| `url_for_landing_page` | The URL for a landing page describing this OA copy. |
| `url_for_pdf` | The URL with a PDF version of this OA copy. |
| `version` | The content version accessible at this location following the DRIVER 2.0 Guidelines (<https://wiki.surfnet.nl/display/DRIVERguidelines/DRIVER-VERSION+Mappings> |

Note that Unpaywall schema is only informally described. Check also
<https://unpaywall.org/data-format>.

## Examples

``` r
if (FALSE) { # \dontrun{
oadoi_fetch("10.1038/nature12373", email = "name@example.com")
oadoi_fetch(dois = c("10.1016/j.jbiotec.2010.07.030",
"10.1186/1471-2164-11-245"), email = "name@example.com")
# flatten OA evidence
roadoi::oadoi_fetch(dois = c("10.1186/s12864-016-2566-9",
                            "10.1103/physreve.88.012814",
                            "10.1093/reseval/rvaa038"),
                   email = "najko.jahn@gmail.com", .flatten = TRUE)

} # }
```
