---
slug: "Unlock Creativity: Transform Your Jupyter Notebook into a Jekyll Gem!"
description: 'Seamlessly Blend Data Brilliance: Pair Jupyter Notebooks with Jekyll for Stunning Visualizations'
keywords: ["Jupyter", " ruby", " Jekyll"]
tags: ["ruby"]
modified: 2024-05-10
modified_by:
  name: Utho
published: 2024-05-10
title: Display Jupyter Notebooks with Jekyll
authors: ["Pawan Kumar"]
---

Jekyll, a static site generator powered by Ruby and seamlessly integrated with Github Pages, simplifies sharing data analysis and visualizations. With Github handling hosting, users can effortlessly showcase their work. Plus, Jekyll offers an array of themes and plugins, eliminating the need for complex web development.

This tutorial will walk you through installing and configuring Jekyll to showcase various outputs from Jupyter notebooks.

## Before You Begin

Begin by acquainting yourself with our [Getting Started](/docs/products/platform/get-started/) guide and follow the steps to set up your Utho's hostname and timezone.

Throughout this guide, we'll utilize sudo wherever applicable. Follow the relevant sections of our [Securing Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/) to establish a standard user account.

Start by updating your system:

             sudo apt update && sudo apt upgrade

Ensure GCC and Make are installed, especially if they're not included in your distribution by default.

            sudo apt install gcc make

## Install Ruby and Jekyll

Install Ruby Version Manager (RVM), a recommended choice for several reasons:

- Eliminates the need for `sudo` when installing gems.
- Simplifies managing multiple sets of gems on a single machine.
- Allows seamless switching between different Ruby versions.

Utilize the `software-properties-common` package for hassle-free addition of new PPAs.

        sudo apt install software-properties-common

Add the RVM repository to the PPA list.

        sudo apt-add-repository -y ppa:rael-gc/rvm

Update the PPA list to access available packages and install RVM.

        sudo apt update && sudo apt install rvm

Upon installation, the terminal will display the newly created group. Exit the terminal session and SSH back into the Utho.

             Creating group 'rvm'

            Installing RVM to /usr/share/rvm/
            Installation of RVM in /usr/share/rvm/ is almost complete:

* First you need to add all users that will be using rvm to 'rvm' group,
Add all users who will use RVM to the 'rvm' group. After logout and login, any user utilizing RVM will operate with `umask u=rwx,g=rwx,o=rx`.

* To begin using RVM, execute `source /etc/profile.d/rvm.sh` in all open shell windows. In rare cases, you may need to reopen all shell windows.


Install Ruby:

        rvm install ruby

Utilize `gem` to download Jekyll and Bundler.

        gem install jekyll bundler

### Create a New Blog

Kickstart a new blog, using `exampleblog` in this guide.

        jekyll new exampleblog

Navigate to the `exampleblog` directory. While Jekyll provides a scaffold for a blog, create an `assets` folder to house images, CSS, and JS files.

        cd exampleblog
        mkdir -p assets/images

Arrange the directory structure as follows:

        exampleblog/
        ├── 404.html
        ├── about.md
        ├── assets
        │   └── images
        ├── _config.yml
        ├── Gemfile
        ├── Gemfile.lock
        ├── index.md
        └── _posts
            └── 2017-10-10-welcome-to-jekyll.markdown

Launch the Jekyll server. Access your Utho's public IP address through a web browser (using port `4000`) to preview the site. A default first post should be visible.

        bundle exec jekyll serve --host=0.0.0.0

Important: Upon starting the Jekyll server, a new _site folder will be generated. Avoid storing files in this folder as it gets rebuilt every time changes are made to the site.

## Configure Jupyter Notebook

If Anaconda with Jupyter is not installed on your system yet, follow these steps to set up a notebook that generates sample output, exportable to your Jekyll blog.

You can perform these steps either from your local machine or from the Utho used for your Jekyll blog. When using a Utho, you can utilize [ngrok](https://ngrok.com/)  to view your notebook.


If Anaconda is not installed on your system, download and install it:

        curl -O https://repo.continuum.io/archive/Anaconda3-5.0.0.1-Linux-x86_64.sh
        bash Anaconda3-5.0.0.1-Linux-x86_64.sh

Follow the installation prompts for Anaconda.

Create an Anaconda environment for Jupyter with R. Replace `data-notebooks` in the command below with a suitable environment name:

        conda create --name data-notebooks
        source activate data-notebooks

Launch the Jupyter notebook:

        jupyter notebook

## Export Jupyter Notebook as Markdown

This section showcases various features of a Jupyter Notebook that can be rendered as HTML on a Jekyll blog. It covers four types of outputs from a Jupyter Notebook cell: MathJex via LaTeX in Markdown, an HTML table, console output, and graphs from a plotting function. The Iris dataset serves as an example to generate the output in this guide.

Open the notebook you're interested in or use the code provided below to create a sample notebook. Execute all relevant cells to display the desired output on your Jekyll blog. Navigate to `File > Download As > Markdown (.md)`. The Markdown file will be saved to the default Downloads folder of your browser.

Alternatively, this can be done directly from the command line. Besides creating `example_notebook.md`, graphics are also saved in a separate `example_notebook_files` folder.

        jupyter nbconvert --to markdown /path/to/example_notebook.ipynb

Below is the demonstration code used in this guide:

             {{< file "example.ipynb" py >}}
            \begin{equation*}
            \mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
            \mathbf{i} & \mathbf{j} & \mathbf{k} \\
            \frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
            \frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0
            \end{vmatrix}
            \end{equation*}

            import pandas as pd
            import seaborn as sns
            %matplotlib inline

            url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
            names = ['sepal-length', 'sepal-width', 'petal-length', 'petal-width', 'class']
            iris = pd.read_csv(url, names=names)

            iris.head()
            iris.describe()

            sns.pairplot(x_vars=["petal-length"], y_vars=["petal-width"], data=iris, hue="class", size=10)

            {{< /file >}}

Inside the `_posts` folder of the Jekyll project, create a new Markdown file named `YYYY-MM-DD-example-post.md`. Incorrect date formatting may result in the post not being displayed on the blog:

        touch YYYY-MM-DD-example-post.md

The Markdown file should start with three dashes and include headers providing information for Jekyll to populate the post with appropriate page data. The date must adhere to the specified format. Hours, minutes, seconds, and timezone adjustment are optional:

           {{< file "YYYY-MM-DD-example-post.md" yaml >}}
           ---
            layout: post
            title:  "Awesome Data Visualization"
            date:   2017-10-10 12:07:25 +0000
            categories:
            - data
            ---

           {{< /file >}}

Copy the contents of the Markdown file exported from Jupyter into the new post.

To accomplish this from the command line, use:

         cat example_notebook.md | tee -a exampleblog/_posts/YYYY-MM-DD-example-post.md

## Modify Markdown Files

Upon visiting your Jekyll blog in a browser, you'll notice a link to the title of the new post ("Awesome Data Visualization" in our example). However, you might observe that some of the output isn't formatted correctly. Depending on the content, there may be characters that need to be escaped. Consult the [Jekyll documentation](https://jekyllrb.com/docs/posts/) for guidance on escaping characters and formatting blocks.

The sections below detail how to adjust and style tables and images to enhance their presentation.

### Extend Default SCSS

Jupyter's tabular output is converted into an HTML table. This section explains how to extend the theme's SCSS to style tables.

Within `/exampleblog/assets`, create a new file named `main.scss`. This file imports the existing minima theme SCSS and introduces additional styling:

          {{< file "main.scss" css >}}
         ---
         ---
         @import "minima";

         p, blockquote, ul, ol, dl, li, table, pre {
         margin: 15px 0; }

         table {
         padding: 0; }
         table tr {
         border-top: 1px solid #cccccc;
         background-color: white;
         margin: 0;
         padding: 0; }
         table tr:nth-child(2n) {
         background-color: #f8f8f8; }
         table tr th {
         font-weight: bold;
         border: 1px solid #cccccc;
         text-align: left;
         margin: 0;
         padding: 6px 13px; }
         table tr td {
         border: 1px solid #cccccc;
         text-align: left;
         margin: 0;
         padding: 6px 13px; }
         table tr th :first-child, table tr td :first-child {
         margin-top: 0; }
         table tr th :last-child, table tr td :last-child {
         margin-bottom: 0; }

        {{< /file >}}

The newly defined styles will be applied to the HTML table.

### Add an Image in Jekyll

Integrating images through Markdown necessitates storing the images in the project directory.

Transfer all images exported from Jupyter into the `/assets/images` folder.

Adjust the image references in the Markdown to reflect the correct path. Enclose the path within double curly braces and quotes.

           {{< file "YYYY-MM-DD-example-post.md" >}}
![png]({{ "/assets/images/example_notebook_5_0" }})
           {{< /file >}}

Graphs with legends that have elongated dimensions can also be displayed.

Note: This is just a demonstration. Incorporating interactive graphs using JavaScript libraries falls beyond the scope of this guide.

### Use a CDN to Support MathJax

Content Delivery Networks (CDNs) offer a convenient way to enhance website functionality without requiring additional software downloads. This section explains how to create a custom header for posts.

 To enable Jekyll to convert LaTeX to PNG, utilize a CDN provided by MathJax. Copy the HTML tag below and paste it beneath the metadata section of `YYYY-MM-DD-example-post.md`:

        <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

The default Jekyll `minima` theme includes the `_layouts` and `_includes` directories packaged with the gem. Access this directory by:

        cd $(bundle show minima)

  Note: The directory tree also contains other HTML files for integrating Disqus and Google Analytics.

        minima-2.1.1/
        ├── assets
        │   └── main.scss
        ├── _includes
        │   ├── disqus_comments.html
        │   ├── footer.html
        │   ├── google-analytics.html
        │   ├── header.html
        │   ├── head.html
        │   ├── icon-github.html
        │   ├── icon-github.svg
        │   ├── icon-twitter.html
        │   ├── icon-twitter.svg
        │   └── scripts.html
        ├── _layouts
        │   ├── default.html
        │   ├── home.html
        │   ├── page.html
        │   └── post.html
        ├── LICENSE.txt
        ├── README.md
        └── _sass
            ├── minima
                │   ├── _base.scss
                    │   ├── _layout.scss
                        │   └── _syntax-highlighting.scss
                            └── minima.scss

Important: The default theme is installed as a gem. Any other `_layouts` or `_includes` folder in the project root will override the theme.

Within the `_includes` directory of the minima theme, create a new `scripts.html` file. Utilize Liquid templating to incorporate logic for checking a `mathjax` header in a post:


           {{< file "_includes/scripts.html" html >}}
          {% if page.mathjax %}
          <script type="text/javascript" async
        src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
            </script>
            {% endif %}
            {{< /file >}}

Integrate templating into `_layouts/post.html` to include `scripts.html` in posts:

              {{< file "_layouts/post.html" >}}
              ---
              layout: default
              ---

             {% include scripts.html %}

             {{< /file >}}

Edit the header of `/exampleblog/_posts/YYYY-MM-DD-example-post.md` with `mathjax: true`. Enclose LaTeX within `$$` to create a math block. Ensure to include the two lines of `---`:


            {{< file "YYYY-MM-DD-example-post.md" yaml >}}
            ---
            layout: post
            mathjax: true
            title:  "Awesome Data Visualization"
            date:   2017-10-10 12:07:25 +0000
            categories: data
            ---

            $$
            \begin{equation*}
            \mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
            \mathbf{i} & \mathbf{j} & \mathbf{k} \\
            \frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
            \frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0
            \end{vmatrix}
            \end{equation*}
            $$

            {{< /file >}}

The browser should utilize MathJax to display output similar to a Jupyter Notebook.

