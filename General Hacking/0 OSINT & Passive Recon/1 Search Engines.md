# OSINT 

Tags: #OSINT #GoogleDorks #GoogleHacking 

## Search Engine Operators

```bash 
❯ https://www.google.com/
❯ https://www.google.com/advanced_search
❯ https://www.exploit-db.com/google-hacking-database

❯ https://www.bing.com/
❯ https://www.bruceclay.com/blog/bing-google-advanced-search-operators/

❯ https://duckduckgo.com/
❯ https://duckduckgo.com/duckduckgo-help-pages/results/syntax/

❯ https://www.baidu.com/

❯ https://yandex.com/
```

## Google Dorks

```
❯ https://www.googleguide.com/print/adv_op_ref.pdf

site:reddit.com             # Obtendremos resultados con ese sitio web en especifivo
	site:reddit.com -www   # Obtendremos resultados de 'Subdominios', con '-' quitamos los resultados que no queremos
filetype:pdf                # Obtendremos resultados con archivos 'pdf' 
intext:password             # Obtendremos resultados en donde tendremos la palabra 'password' en el texto 
inurl:password              # Obtendremos resultados en donde tendremos la palabra 'password' en la url 

AND                         # Obtendremos resultados mas especificos con el operador 'AND' 
OR                          # Obtendremos diferentes resultados con la condicion del operador 'OR'
" "                         # Resultados especificos con " "
* word                      # Wildcard, hace match con todo lo que encuentre antes de la palabra 'word'
```