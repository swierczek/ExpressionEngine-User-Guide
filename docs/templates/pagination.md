<!--
    This source file is part of the open source project
    ExpressionEngine User Guide (https://github.com/ExpressionEngine/ExpressionEngine-User-Guide)

    @link      https://expressionengine.com/
    @copyright Copyright (c) 2003-2020, Packet Tide, LLC (https://packettide.com)
    @license   https://expressionengine.com/license Licensed under Apache License, Version 2.0
-->

# Pagination

[TOC]

Pagination works identically across all first-party modules and allows you to display a limited number of entries and then automatically link to the next set. That way you can, for example, show comments 1-10 on the first page and automatically link to pages that display 11-20, 21-30, etc.

You have two choices as to the style of the navigation element. The first method would look something like this:

    Page 27 of 344 pages  « First  <  11 12 13 14 15 >  Last »

The second method is a more traditional "next page" / "previous page" output:

    Previous Page | Next Page

Pagination will also automatically restrict itself to any category you're currently viewing. So if you have a category specified in your channel entries tag or you are viewing the entries of a category, then the pagination links will automatically restrict themselves to only entries in that category.

NOTE: **Note:** If there is not enough content for more than one page, nothing will be output by these tags.

## Example Code

Here are two basic code examples, one for each of the methods mentioned above. Information about the variables and parameters are covered later.

    {exp:channel:entries channel="news" orderby="date" sort="desc" limit="1" paginate="bottom"}
        <h2>{title}</h2>
        {summary}
        {body}

        {paginate}
            <p>Page {current_page} of {total_pages} pages {pagination_links}</p>
        {/paginate}
    {/exp:channel:entries}

NOTE: **Note:** It does **not** matter where you put your `{paginate}` code within the channel entries tag. The HTML markup within the `{paginate}` tag pair will be placed in the appropriate location according to what you specify with the `paginate=` parameter (detailed below).

And for the "next/previous" method:

    {exp:comment:entries channel="news" sort="desc" limit="1" paginate="bottom"}
        {comment}
        <p>By {name} on {comment_date format="%Y %m %d"}</p>

        {paginate}
            {if previous_page}
                <a href="{auto_path}">Previous Page</a> &nbsp;
            {/if}
            {if next_page}
                <a href="{auto_path}">Next Page</a>
            {/if}
        {/paginate}
    {/exp:comment:entries}

NOTE: **Note:** You can have multiple `{paginate}` blocks with different content, but you'll need to use `paginate=inline`.

## Parameters

Pagination tag pair parameters should be used in the enclosing tag. For example:

    {exp:comment:entries limit="1" paginate="bottom"}

### paginate=

This parameter determines where the pagination code will appear:

    paginate="top" paginate="bottom"  paginate="both"  paginate="inline"  paginate="hidden"

1.  `top`: The navigation text and links will appear _above_ your list of entries.
2.  `bottom`: The navigation text and links will appear _below_ your list of entries.
3.  `both`: The navigation text and links will appear both above and below your list of entries.
4.  `inline`: The navigation text and links will appear within the list of entries for each entry.
5.  `hidden`: The navigation text and links will **not** appear, but your entries will be paginated. (This is useful for things like JSON.)

If no parameter is specified, the navigation block will default to the "bottom" behavior.

### paginate_base=

    paginate_base="site/index"

This tells ExpressionEngine to override the normal pagination link locations and point instead to the explicitly stated template group and template.

## Variable Pairs

### paginate

The opening and closing tags for pagination. This can to be used in conjunction with the [paginate=](#paginate) parameter to determine where the contents of this tag will appear. See below for the available variables for use inside this tag. This tag is wrapped around either the single variables (see below) or the next/previous variable pairs.

    {paginate}  {/paginate}

### pagination_links

This variable shows the current page you are on as well as "surrounding" pages in addition to links for next/previous pages and first/last pages.

You can use `{pagination_links}` in two ways. It can be used as a single variable:

    {paginate}
      {pagination_links}
    {/paginate}

And the output looks like this:

    « First  <  11 12 13 14 15 >  Last »

When used as a pair, you have a lot more flexibility with the markup:

    {paginate}
      {pagination_links}
        <ul>
          {first_page}
            <li><a href="{pagination_url}" class="page-first">First Page</a></li>
          {/first_page}

          {previous_page}
            <li><a href="{pagination_url}" class="page-previous">Previous Page</a></li>
          {/previous_page}

          {page}
            <li><a href="{pagination_url}" class="page-{pagination_page_number} {if current_page}active{/if}">{pagination_page_number}</a></li>
          {/page}

          {next_page}
            <li><a href="{pagination_url}" class="page-next">Next Page</a></li>
          {/next_page}

          {last_page}
            <li><a href="{pagination_url}" class="page-last">Last Page</a></li>
          {/last_page}
        </ul>
      {/pagination_links}
    {/paginate}

There are one parameter, two variables, five variable pairs, and one conditional variable available when using the {pagination_links} pair.

#### Parameters

##### page_padding

    {pagination_links page_padding="2"}

`page_padding` defaults to 2 if left unset. `page_padding` will allow you to determine how many pages are shown on either side of the current page.

NOTE: **Note:** If you're paginating over 10 items and showing 1 item per page, if you're on the first page, you'd normally see this:

    *1*  2  3  Next Page  Last Page

If you changed `page_padding` to another value, say 5, you'd see more page numbers:

    *1*  2  3  4  5  6  Next Page  Last Page

If you later changed to page 4, you'd see this:

    Previous Page  1  2  3  *4*  5  6  7  8  9  Next Page  Last Page

##### always_show_first_last

    {pagination_links always_show_first_last="yes"}

Set this parameter to `yes` if you always want to see the links for the first and last pages.

NOTE: **Note:** If left unset or set to `no`, first and last links will only be shown if the first and/or last page's number isn't shown.

For example, if you have 5 pages and `pagination_links` is set at 2 and you're currently on the 3rd page you won't see a first or last page link because all five pages are shown:

    Previous Page  1  2  *3*  4  5  Next Page

If you changed `pagination_links` to 1, then you'd see both first and last links because the page numbers **are not** shown:

    First Page  Previous Page  2  *3*  4  Next Page  Last Page

#### Variables

##### pagination_page_number

    {pagination_page_number}

Outputs the page number associated with the current page in the {pagination_links} tag pair.

##### pagination_url

    {pagination_url}

Outputs the URL associated with the current page in the {pagination_links} tag pair.

#### Variable Pairs

These four variable pairs can be used to display specific pages within the pagination:

NOTE: **Note:** The markup within the `first_page` and `last_page` variable pairs will only display when there are at least 4 pages of content.

    {first_page}
      <li><a href="{pagination_url}" class="page-first">First Page</a></li>
    {/first_page}

    {previous_page}
      <li><a href="{pagination_url}" class="page-previous">Previous Page</a></li>
    {/previous_page}

    {next_page}
      <li><a href="{pagination_url}" class="page-next">Next Page</a></li>
    {/next_page}

    {last_page}
      <li><a href="{pagination_url}" class="page-last">Last Page</a></li>
    {/last_page}

The `{page}` variable pair can be used to display standard pagination links:

    {page}
      <li><a href="{pagination_url}" class="page-{pagination_page_number}">{pagination_page_number}</a></li>
    {/page}

#### Conditional Variables

Check and see if the current {page} link is the current page.

    {if current_page}class="current"{/if}

### if next_page

This tag will conditionally display the code inside the tag if there is a "next" page. If there is no next page then the content simply will not be displayed.

    {if next_page}  {/if}

### if previous_page

This tag will conditionally display the code inside the tag if there is a "previous" page. If there is no previous page then the content simply will not be displayed.

    {if previous_page}  {/if}

## Variables

These individual variables are for use inside the [{paginate}](#paginate-1) tag pair.

### auto_path

The {auto_path} variable is used inside of the [{if next_page}](#if-next_page) and [{if previous_page}](#if-previous_page) variable pairs. It is dynamically replaced with the correct path to the next/previous page. Unlike other "path" variables, this variable does **not** require the Template_Group/Template to be specified.

    {auto_path}

### current_page

This variable is replaced by the page number of the current page you are viewing.

    {current_page}

### total_pages

The total number of pages you have.

    {total_pages}
