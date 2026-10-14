# Book Converter

Turns books (PDF, EPUB, DOCX, HTML, TXT, Markdown) into clean, chapter-split JSON for
[Book Reader](https://github.com/NabilPervez/book-reader) — live at https://read-more-book-reader.netlify.app/

- Finds chapters from the book's own table of contents, falling back to headings, then even chunks
- PDF cleanup: strips running headers/footers and page numbers, rejoins hyphenated words, merges paragraphs split across pages
- OCRs scanned PDFs automatically (needs `brew install tesseract`)
- Removes Project Gutenberg boilerplate

## Setup

```bash
pip3 install -r requirements.txt   # PyMuPDF, only needed for PDFs
```

## Use

```bash
# drop files in ./books, then:
python3 book_converter.py                                   # → ./library
python3 book_converter.py --out ../book-reader/public/library   # straight into the reader
python3 book_converter.py books/x.pdf --title "Title" --author "Name"
python3 book_converter.py books/x.pdf --split words:3000    # no usable chapters? cut evenly
python3 book_converter.py books/scan.pdf --ocr always --lang eng+ara
python3 book_converter.py --list --out ../book-reader/public/library
python3 book_converter.py --remove <book-id> --out ../book-reader/public/library
```

Then commit and push `book-reader` — Netlify redeploys with the new book.

## Output format

```
library/index.json              list of books
library/<book>/book.json        metadata + table of contents
library/<book>/<chapter>.json   {id, title, blocks: [{t, text, page?, n?}]}
```
`t` is `p`, `h2`, `h3`, `quote`, `li` or `bullet`; text may contain `*italic*` / `**bold**`.
Only the Python standard library is used except for PDFs.
