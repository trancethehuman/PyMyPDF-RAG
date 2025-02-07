.. include:: header.rst


API
===========================================================================



.. property:: version

    Prints the version of the library.

.. method:: to_markdown(doc: pymupdf.Document | str, *, pages: list | range | None = None, hdr_info: Any = None, write_images: bool = False, dpi: int = 150, margins=(0, 50, 0, 50), page_chunks: bool = False, page_width: float = 612, page_height: float = None, table_strategy="lines_strict", graphics_limit: int = None) -> str | list[dict]

    Read the pages of the file and outputs the text of its pages in |Markdown| format. How this should happen in detail can be influenced by a number of parameters. Please note that there exists **support for building page chunks** from the |Markdown|  text.

    :arg Document,str doc: the file, to be specified either as a file path string, or as a |PyMuPDF| Document (created via `pymupdf.open`). In order to use `pathlib.Path` specifications, Python file-like objects, documents in memory etc. you **must** use a |PyMuPDF| Document.

    :arg list pages: optional, the pages to consider for output (caution: specify 0-based page numbers). If omitted all pages are processed.

    :arg hdr_info: optional. Use this if you want to provide your own header detection logic. This may be a callable or an object having a method named `get_header_id`. It must accept a text span (a span dictionary as contained in `extractDict <https://pymupdf.readthedocs.io/en/latest/textpage.html#span-dictionary>`_) and a keyword parameter "page" (which is the owning `Page <https://pymupdf.readthedocs.io/en/latest/page.html>`_ object). It must return a string "" or up to 6 "#" characters followed by 1 space. If omitted, a full document scan will be performed to find the most popular font sizes and derive header levels based on them. To completely avoid this behavior specify `hdr_info=lambda s, page=None: ""` or `hdr_info=False`.

    :arg bool write_images: when encountering images or vector graphics, PNG images will be created from the respective page area and stored in the folder of the document. Markdown references will be generated pointing to these images. Any text contained in these areas will not be included in the text output (but appear as part of the images). Therefore, if your document has text written on full page images, make sure to set this parameter to `False`.

    :arg int dpi: specify the desired image resolution in dots per inch. Relevant only if `write_images=True`. Default value is 150.

    :arg float,list margins: a float or a sequence of 2 or 4 floats specifying page borders. Only objects inside the margins will be considered for output.
    
        * `margin=f` yields `(f, f, f, f)` for `(left, top, right, bottom)`.
        * `(top, bottom)` yields  `(0, top, 0, bottom)`.
        * To always read full pages, use `margins=0`.

    :arg float page_width: specify a desired page width. This is ignored for documents with a fixed page width like PDF, XPS etc. **Reflowable** documents however, like e-books, office or text files have no fixed page dimensions and by default are assumed to have Letter format width (612) and an **"infinite"** page height. This means that the full document is treated as one large page.

    :arg float page_height: specify a desired page height. For relevance see the `page_width` parameter. If using the default `None`, the document will appear as one large page with a width of `page_width`. Consequently in this case, no markdown page separators will occur (except the final one), respectively only one page chunk will be returned.
    
    :arg str table_strategy: table detection strategy. Default is `"lines_strict"` which ignores background colors. In some occasions, other strategies may be more successful, for example `"lines"` which uses all vector graphics objects for detection.

    :arg int graphics_limit: use this to limit dealing with excess amounts of vector graphics elements. Typically, scientific documents or pages simulating text using graphics commands may contain tens of thousands of these objects. As vector graphics are used for table detection mainly, analyzing pages of this kind may result in excessive runtimes. You can exclude problematic pages via `graphics_limit=5000`. The respective pages will then be ignored and be represented by one message line in the output text.

    :arg bool page_chunks: if `True` the output will be a list of `Document.page_count` dictionaries (one per page). Each dictionary has the following structure:

        - **"metadata"** - a dictionary consisting of the document's metadata `Document.metadata <https://pymupdf.readthedocs.io/en/latest/document.html#Document.metadata>`_, enriched with additional keys **"file_path"** (the file name), **"page_count"** (number of pages in document), and **"page_number"** (1-based page number).

        - **"toc_items"** - a list of Table of Contents items pointing to this page. Each item of this list has the format `[lvl, title, pagenumber]`, where `lvl` is the hierachy level, `title` a string and `pagenumber` the 1-based page number.

        - **"tables"** - a list of tables on this page. Each item is a dictionary with keys "bbox", "row_count" and "col_count". Key "bbox" is a `pymupdf.Rect` in tuple format of the table's position on the page.

        - **"images"** - a list of images on the page. This a copy of page method `get_image_info <https://pymupdf.readthedocs.io/en/latest/page.html#Page.get_image_info>`_. Please see there for a full description of items.

        - **"graphics"** - a list of vector graphics rectangles on the page. This is a list of boundary boxes of clustered vector graphics as delivered by method `cluster_drawings <https://pymupdf.readthedocs.io/en/latest/page.html#Page.cluster_drawings>`_.

        - **"text"** - page content as Markdown text.

    :returns: Either a string of the combined text of all selected document pages or a list of dictionaries.

.. method:: LlamaMarkdownReader(*args, **kwargs)

    Create a `pdf_markdown_reader.PDFMarkdownReader` using the `LlamaIndex <https://pypi.org/project/llama-index/>`_ package. Please note that this package will **not be installed** when installing **pymupdf4llm**.

    For details on the possible arguments, please consult the LlamaIndex documentation [#f1]_.

    :raises: NotImplementedError: Please install required 'llama_index'.
    :returns: a `pdf_markdown_reader.PDFMarkdownReader` and issues message "Successfully imported LlamaIndex". Please note that this method needs several seconds to execute. For details on using the markdown reader please see below.

----


.. class:: pdf_markdown_reader.PDFMarkdownReader
   
    .. method:: load_data(file_path: Union[Path, str], extra_info: Optional[Dict] = None, **load_kwargs: Any) -> List[LlamaIndexDocument]

        This is the only method of the markdown reader you should currently use to extract markdown data. Please in any case ignore methods `aload_data()` and `lazy_load_data()`. Other methods like `use_doc_meta()` may or may not make sense. For more information, please consult the documentation of LlamaIndex [#f1]_.

        Under the hood the method will execute `to_markdown()`.

        :returns: a list of `LlamaIndexDocument` documents - one for each page.


.. rubric:: Footnotes

.. [#f1] `LlamaIndex documentation <https://docs.llamaindex.ai/en/stable/>`_


.. include:: footer.rst





