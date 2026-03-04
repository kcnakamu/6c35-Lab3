<script>
import { base } from "$app/paths";
import { page } from "$app/stores";


let pages = [
  {url: "/", title: "About"},
  {url: "/projects", title: "Projects"},
  {url: "/resume", title: "Resume"},
  {url: "/contact", title: "Contact"},
  {url: "https://github.com/kcnakamu", title: "Github"},
];

let colorScheme = "light dark";
let root = globalThis.document?.documentElement;
$: root?.style.setProperty("color-scheme", colorScheme);

let localStorage = globalThis.localStorage ?? {};
if (localStorage.colorScheme) { 
  colorScheme = localStorage.colorScheme;
}
$: localStorage.colorScheme = colorScheme;

</script>

<style>
ul, 
li {
    display: contents;
}


.Menu {
    display: flex;
}



.Menu a {
    flex: 1; /* step 2.2 for each element to take the same space  */
    text-decoration: none; /* Remove the underline from the links by setting */
    color: inherit; /* Add here color */
    text-align: center; /* Add here text-align */
    padding: 0.5em; /* Add here padding */
    margin-bottom: 10px;/* Add here margin-bottom */
    border-bottom-width: 1px;
    border-bottom-style: solid;
    border-bottom-color: oklch(50% 10% 200 / 40%);

}

.Menu a.current {
    border-bottom-width: 0.4em;
    padding-bottom: 0px;
    /* Add here what your current page should look like at the navigation bar (highlight)*/
}

.Menu a:hover {
    /* Add here hover effects */
    border-bottom: 0.4em solid var(--color-accent);
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
    padding-bottom: 0px;
}

.color-scheme-switch {
    position: absolute;
    top: 1rem;
    right: 1rem;
    display: inline-flex;
    gap: 4px;
    font-size: 80%;
}
</style>


<label class="color-scheme-switch">
    Theme:
    <select bind:value={ colorScheme }>
        <option value="light dark">Automatic</option>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
    </select>
</label>


<nav class="Menu">
    <ul>
        {#each pages as p}
        <li>
            <a href={base + p.url}
            <a 
            href={p.url.startsWith("http") ? p.url :base + p.url}
            class:current={p.url === "/" 
                ? $page.url.pathname === (base + "/") 
                : $page.url.pathname.startsWith(base + p.url)}
            target={p.url.startsWith("http") ? "_blank": null}
            >
  {p.title}
</a>

        </li>
        {/each}
    </ul> 
</nav>

<slot />

