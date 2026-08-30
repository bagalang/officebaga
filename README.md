# officebaga

**Office documents** for Baga — DOCX/XLSX/ODT/ODS (full path) + legacy DOC/XLS
(OLE2 probe). **No presentations.**

| | |
|--|--|
| **sandak** | `officebaga` **0.3.0** |
| **Deps** | `zipbaga`, `xmlbaga`, `bufbaga`, `mdbaga`, `std` |
| **Design** | [spec](../../docs/superpowers/specs/2026-08-06-officebaga-design.md) |
| **Plan** | [plan](../../docs/superpowers/plans/2026-08-06-officebaga.md) · [gaps](gaps.md) |
| **Tests** | `tests/office_test.baga` |

This repository is the package. The compiler, `std`, `zipbaga`,
`xmlbaga`, `bufbaga`, and `mdbaga` stay in the baga language monorepo.
Check this tree out as `app-product/officebaga` there (git submodule)
so path deps and `-I app-product` keep working.

## Checkout

Inside a baga language clone:

```bash
git submodule update --init --recursive
# or, first time from a fresh baga tree without the submodule recorded:
git clone git@github.com:bagalang/officebaga.git app-product/officebaga
```

`sandak.toml` keeps path deps so the shared packages stay in baga.
`reportbaga` still depends on `../officebaga`. `tests/office_test.baga`
stays in baga.

## Formats

| Format | Extract | Create | Edit | Notes |
|--------|---------|--------|------|-------|
| DOCX | ✅ | ✅ + md→docx | ✅ replace_text | ZIP deflate |
| XLSX | ✅ | ✅ | ✅ (sharedStrings/sheet XML) | числа като `t="n"`, не текст |
| ODT | ✅ | ✅ | ✅ content.xml | mimetype stored |
| ODS | ✅ | ✅ | ✅ | числа: `float` + текст със запетая |
| DOC | ⚠ ASCII probe | — | — | OLE2/CFB |
| XLS | ⚠ ASCII probe | — | — | OLE2/CFB |
| PPTX | ❌ | — | — | rejected |

## API

```baga
import "officebaga/office.baga"

let r = office_open("report.docx")?
print(office_plain_text(r.doc))
print(office_to_markdown(r.doc))

// edit (ZIP packages)
let e = office_replace_text(r.doc, "Hello", "Zdravei")
office_save(e.doc, "out.docx")?

// create
office_docx_bytes("Title", "Body")
office_md_to_docx_bytes("# Hi\n\nPara\n")
// xlsx/ods: "1056.9" / "1056,9" → numeric cell; "0000000001" stays text

// legacy OLE
let streams = office_ole_streams(doc)  // if .doc/.xls
```

## CLI

```bash
cd app-product/officebaga && sandak build
baga -I ../.. -I .. demo.baga text file.docx
baga -I ../.. -I .. demo.baga replace file.docx Hello Zdravei out.docx
baga -I ../.. -I .. demo.baga streams legacy.doc
baga -I ../.. -I .. demo.baga md-docx out.docx notes.md
```

## Layout

```
officebaga/
├── office.baga
├── edit/edit.baga      # set_part / replace_text
├── ole/cfb.baga        # OLE2 container
├── ir/ opc/ core/ docx/ xlsx/ odf/ convert/
└── fixtures/min.doc
```

## License

[MIT](LICENSE) — Copyright (c) 2026 Dim Gigov.
