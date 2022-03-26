---
title: "Demo Post"
date: 2021-11-23T00:11:54-05:00
---

Just to make sure the site works and audit check doesn't fail when there is content with the audit check.

   ```bash
   #!/bin/bash
   set -o pipefail
   
   HUGO_BUILD_URL="$1"
   
   # Should only occur using Netlify CLI
   if [ -z "HUGO_BUILD_URL" ]
   then
       HUGO_BUILD_URL="https://www.example.com/"
   fi
   
   shift
   
   ( set -o pipefail && cd themes/hugo-geekdoc && npm install --no-save && npx gulp default )
   
   HUGO_MINIFY_TDEWOLFF_HTML_KEEPCOMMENTS=true HUGO_ENABLEMISSINGTRANSLATIONPLACEHOLDERS=true hugo ${HUGO_BUILD_URL:+-b $HUGO_BUILD_URL} && grep -inorE "<\!-- raw HTML omitted -->|ZgotmplZ|hahahugo|\[i18n\]|\(<nil>\)" public/; RET="$?"
   
   if [ "$RET" != "0" ]
   then
       hugo --gc ${HUGO_BUILD_URL:+-b $HUGO_BUILD_URL} --cleanDestinationDir "$@"; RET=$?
   else
       cd ..
   fi
   
   exit "$RET"
   ```

   ``_scripts/hugo-serve``

   ```bash
   #!/bin/bash
   
   ( set -o pipefail && cd themes/hugo-geekdoc && npm install --no-save && npx gulp default )
   
   HUGO_MINIFY_TDEWOLFF_HTML_KEEPCOMMENTS=true HUGO_ENABLEMISSINGTRANSLATIONPLACEHOLDERS=true hugo ${HUGO_BUILD_URL:+-b $HUGO_BUILD_URL} && grep -inorE "<\!-- raw HTML omitted -->|ZgotmplZ|hahahugo|\[i18n\]" public/; RET="$?"
   
   if [ "$RET" != "0" ]
   then
       hugo server --poll 700ms -b http://localhost:8888/ -w "$@"
   else
       cd ..
   fi
   
   exit 0
   ```
