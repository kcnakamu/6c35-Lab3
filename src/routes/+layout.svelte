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


</script>


<nav class="Menu">
    <ul>
        {#each pages as p}
        <li>
            <a href={base + p.url}
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