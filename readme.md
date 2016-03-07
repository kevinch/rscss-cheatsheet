# rscss Cheatsheet
> Styling CSS without losing your sanity

A single page explanation for RSCSS. 

All credits to Rico Sta. Cruz: [ricostacruz.com](http://ricostacruz.com/) | Github: [@rstacruz](https://github.com/rstacruz) | Twitter: [@rstacruz](https://twitter.com/rstacruz)

## 0. Summary

+ Think in components, named with 2 words (.screenshot-image)
+ Components have elements, named with 1 word (.blog-post > .title)
+ Name variants with a dash prefix (.shop-banner.-with-icon)
+ Components can nest
+ Remember you can extend to make things simple
---
## 1 Components
### 1.1 About components
Think in components. Consider each piece of your UI as an individual "component".

#### Naming components
Components will be named with **at least two words**, separated by a **dash**. Examples of components:
+ A like button (`.like-button`)
+ A search form (`.search-form`)
+ A news article card (`.article-card`)

### 1.2 Elements
Elements are _things_ inside your component.

#### Naming elements
Each component may have elements. They should have classes that are only one word.

    .search-form {
        > .field { /* ... */ }
        > .action { /* ... */ }
    }

#### Element selectors
Prefer to use the `>` child selector whenever possible. This prevents bleeding through nested components, and performs better than descendant selectors.

    .article-card {
        .title     { /* okay */ }
        > .author  { /* ✓ better */ }
    }

#### On multiple words
For those that need two or more words, concatenate them without dashes or underscores.

    .profile-box {
        > .firstname { /* ... */ }
        > .lastname { /* ... */ }
        > .avatar { /* ... */ }
    }

#### Avoid tag selectors
Use classnames.

### 1.3 Variants
Components may have variants. Elements may have variants, too.

#### Naming variants
Classnames for variants will be prefixed by a dash (`-`).

    .like-button {
        &.-wide { /* ... */ }
        &.-short { /* ... */ }
        &.-disabled { /* ... */ }
    }

#### Element variants
Elements may also have variants.

    shopping-card {
        > .title { /* ... */ }
        > .title.-small { /* ... */ }
    }

#### Dash prefixes
Dashes are the preferred prefix for variants.
+ It prevents ambiguity with elements.
+ A CSS class can only start with a letter, _ or -.
+ Dashes are easier to type than underscores.
+ It kind of resembles switches in UNIX commands (gcc -O2 -Wall -emit-last).

### 1.4 Nested components
    <div class='article-link'>
        <div class='vote-box'>
        ...
        </div>
        <h3 class='title'>...</h3>
        <p class='meta'>...</p>
    </div>

Sometimes it's necessary to nest components. Here are some guidelines for doing that.

#### Variants
A component may need to appear a certain way when nested in another component. Avoid modifying the nested component by reaching into it from the containing component.

    .article-header {
        > .vote-box > .up { /* ✗ avoid this */ }
    }

Instead, prefer to add a variant to the nested component and apply it from the containing component.

    <div class='article-header'>
        <div class='vote-box -highlight'>
        ...
        </div>
        ...
    </div>
    
    ...

    .vote-box {
        &.-highlight > .up { /* ... */ }
    }

#### Simplifying nested components
Sometimes, when nesting components, your markup can get dirty:

    <div class='search-form'>
        <input class='input' type='text'>
        <button class='search-button -red -large'></button>
    </div>

You can simplify this by using your CSS preprocessor's `@extend` mechanism:

    <div class='search-form'>
        <input class='input' type='text'>
        <button class='submit'></button>
    </div>

    ...

    .search-form {
        > .submit {
            @extend .search-button;
            @extend .search-button.-red;
            @extend .search-button.-large;
        }
    }

### 1.5 Layout
#### Avoid positioning properties
Components should be made in a way that they're reusable in different contexts. Avoid putting these properties in components:

+ Positioning (position, top, left, right, bottom)
+ Floats (float, clear)
+ Margins (margin)
+ Dimensions (width, height)

#### Fixed dimensions
Exception to these would be elements that have fixed width/heights, such as avatars and logos.

#### Define positioning in parents
If you need to define these, try to define them in whatever context they will be in. In this example below, notice that the widths and floats are applied on the list component, not the component itself.

    .article-list {
        & {
            @include clearfix;
        }
        > .article-card {
            width: 33.3%;
            float: left;
        }
    }

    .article-card {
        & { /* ... */ }
        > .image { /* ... */ }
        > .title { /* ... */ }
        > .category { /* ... */ }
    }

### 1.6 Helpers
For general-purpose classes meant to override values, put them in a separate file and name them beginning with an underscore. They are typically things that are tagged with !important. Use them very sparingly.
#### Naming helpers
Prefix classnames with an underscore. This will make it easy to differentiate them from modifiers defined in the component. Underscores also look a bit ugly which is an intentional side effect: using too many helpers should be discouraged.

    <div class='order-graphs -slim _unmargin'>
    </div>

#### Organizing helpers
Place all helpers in one file called helpers. While you can separate them into multiple files, it's very preferrable to keep your number of helpers to a minimum.

---
## 2 CSS structure
### 2.1 One component per file
For each component, place them in their own file.

    /* css/components/search-form.scss */
    .search-form {
        > .button { /* ... */ }
        > .field { /* ... */ }
        > .label { /* ... */ }

        // variants
        &.-small { /* ... */ }
        &.-wide { /* ... */ }
    }

### 2.2 Use glob matching
In sass-rails and stylus, this makes including all of them easy: `@import 'components/*';`

### 2.3 Avoid over-nesting
Use no more than 1 level of nesting. It's easy to get lost with too much nesting.

    /* ✗ Avoid: 3 levels of nesting */
    .image-frame {
        > .description {
            /* ... */

            > .icon {
                /* ... */
            }
        }
    }

    /* ✓ Better: 2 levels */
    .image-frame {
        > .description { /* ... */ }
        > .description > .icon { /* ... */ }
    }
---
## 3 Notes
### 3.1 Pitfalls

#### Bleeding through nested components
Be careful about nested components with elements sharing the same name as elements in its container.

    <article class='article-link'>
        <div class='vote-box'>
            <button class='up'></button>
            <button class='down'></button>
            <span class='count'>4</span>
        </div>

        <h3 class='title'>Article title</h3>
        <p class='count'>3 votes</p>
    </article>

    ...

    .article-link {
        > .title { /* ... */ }
        > .count { /* ... (!!!) */ }
    }

    .vote-box {
        > .up { /* ... */ }
        > .down { /* ... */ }
        > .count { /* ... */ }
    }

In this case, if .article-link > .count did not have the > (child) selector, it will also apply to the .vote-box .count element. This is one of the reasons why child selectors are preferred.

### 3.2 Apprehensions
Read at [rscss.io/apprehensions.html](http://rscss.io/apprehensions.html)

### 3.3 Other Ressources
Read at [rscss.io/other-resources.html](http://rscss.io/other-resources.html)
