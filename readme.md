# RSCSS Cheatsheet
> Styling CSS without losing your sanity
## 1. Components
Components will be named with at least two words, separated by a dash. Examples of components:
+ A like button (.like-button)
+ A search form (.search-form)
+ A news article card (.article-card)
---

## 2. Elements
Elements are things inside your component.

### 2.1 Naming elements
Each component may have elements. They should have classes that are only one word.

    .search-form {
        > .field { /* ... */ }
        > .action { /* ... */ }
    }

### 2.2 Element selectors
Prefer to use the > child selector whenever possible. This prevents bleeding through nested components, and performs better than descendant selectors.

    .article-card {
        .title     { /* okay */ }
        > .author  { /* ✓ better */ }
    }

### 2.3 On multiple words
For those that need two or more words, concatenate them without dashes or underscores.

    .profile-box {
        > .firstname { /* ... */ }
        > .lastname { /* ... */ }
        > .avatar { /* ... */ }
    }

### 2.4 Avoid tag selectors
Use classnames whenever possible. Tag selectors are fine, but they may come at a small performance penalty and may not be as descriptive.

## 3. Variants
Components may have variants. Elements may have variants, too.

### 3.1 Naming variants
Classnames for variants will be prefixed by a dash (-).

    .like-button {
        &.-wide { /* ... */ }
        &.-short { /* ... */ }
        &.-disabled { /* ... */ }
    }

### 3.2 Element variants
Elements may also have variants.

    shopping-card {
        > .title { /* ... */ }
        > .title.-small { /* ... */ }
    }

### 3.3 Dash prefixes
Dashes are the preferred prefix for variants.
+ It prevents ambiguity with elements.
+ A CSS class can only start with a letter, _ or -.
+ Dashes are easier to type than underscores.
+ It kind of resembles switches in UNIX commands (gcc -O2 -Wall -emit-last).

## 4. Nested components
    <div class='article-link'>
        <div class='vote-box'>
        ...
        </div>
        <h3 class='title'>...</h3>
        <p class='meta'>...</p>
    </div>

Sometimes it's necessary to nest components. Here are some guidelines for doing that.

### 4.1 Variants
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

Css:

    .vote-box {
        &.-highlight > .up { /* ... */ }
    }

## 4.2 Simplifying nested components
Sometimes, when nesting components, your markup can get dirty:

