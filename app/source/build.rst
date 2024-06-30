Build
=====

- Move to `app` directory

  .. code-block:: shell

     cd app

- Build source code

  .. code-block:: shell

     make clean html

  A successfull buil should look similar to the following

  .. code-block:: shell

     Removing everything under 'build'...
     Running Sphinx v7.3.7
     making output directory... done
     building [mo]: targets for 0 po files that are out of date
     writing output... 
     building [html]: targets for 4 source files that are out of date
     updating environment: [new config] 4 added, 0 changed, 0 removed
     reading sources... [100%] install
     looking for now-outdated files... none found
     pickling environment... done
     checking consistency... done
     preparing documents... done
     copying assets... copying static files... done
     copying extra files... done
     done
     writing output... [100%] install
     generating indices... genindex done
     writing additional pages... search done
     dumping search index in English (code: en)... done
     dumping object inventory... done
     build succeeded.

     The HTML pages are in build/html.
