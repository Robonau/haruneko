<script lang="ts">
    import {
        Icon,
        Accordion,
        Tile,
        Search
    } from "carbon-components-svelte";
    import Tag from "./Tag.svelte"; 

    import CloseOutline32 from "carbon-icons-svelte/lib/CloseOutline32";
    import CloseFilled32 from "carbon-icons-svelte/lib/CloseFilled32";
    let iconClose:any = CloseOutline32;

    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    import type {IMangaHost} from '../../../engine/MangaProvider';
    import Plugin from "./Plugin.svelte";

    export let pluginlist:Array<IMangaHost>;

    //quickly inline because of dangerous lazyness
    interface ITag {
        category: string;
        label: string;
    }

    //because hardccoding values is da way (Do You Know Da Wae?)
    //will fuse in a single main array with dispatch
    const langTags:Array<ITag>=[
        {category:"lang",label:'English'},
        {category:"lang",label:'French'},
        {category:"lang",label:'multi-lingual'}
    ];
    const typeTags:Array<ITag>=[
        {category:"type",label:'Anime'},
        {category:"type",label:'Manga'},
        {category:"type",label:'Novel'},
        {category:"type",label:'Webtoon'}
    ];
    const otherTags:Array<ITag>=[
        {category:"lang",label:'porn'},
        {category:"lang",label:'raw'},
        {category:"lang",label:'scanlation'},
        {category:"lang",label:'subbed'}
    ];

    let pluginNameFilter = "";
    

    let pluginTagsFilter:Array<ITag>=[];
    
    function addTagFilter(tag:ITag) {
        //todo: check for duplicate
        pluginTagsFilter=[...pluginTagsFilter,tag];
    }
    function removeTagFilter(tag:ITag) {
        pluginTagsFilter = pluginTagsFilter.filter(function(value:any, index, arr){ 
            return tag !== value;
        });
    }
    addTagFilter({"category":"lang","label":"French"});

    let filteredpluginlist:Array<IMangaHost>= pluginlist;
    $: filteredPluginlist = pluginlist.filter(item => {
            let conditions:Array<boolean>=[];
            if(pluginNameFilter!=='')
            {
                conditions.push(item.Title.toLowerCase().indexOf(pluginNameFilter.toLowerCase()) !== -1);
            }
            if (pluginTagsFilter.length>0)
            {
                // Quick test tag filtering using language property
                // Should be a test if all selected tags are in the tags of plugin
                conditions.push (item.Language!==undefined ? pluginTagsFilter.find(element => element.label ===item.Language) !== undefined : true);
            }
            return !conditions.includes(false); 
        }
    );

</script>

<style>
    .content {
        text-align: center;
        overflow-y: scroll;
        overflow-x: hidden;
        height:calc(100vh - 7.5em);
    }
    .topleft{
        position:relative;
    }
    .close {
        display:inline-block;
    }
    .title{
        display:inline-block;
    }
    .tags { width : 100% }
    .lang{
        display:inline-block;
        width:33%;
    }
    .type {
        display:inline-block;
        width:33%;
    }
    .other {
        display:inline-block;
        width:33%;
    }
    .filter {
        display:inline-block;
    }
    .selectedtags{
        display:inline-block;
    }

</style>
<div class="topleft">
    <div class="close">
        <Icon 
            render={iconClose} 
            on:mouseover={() => {iconClose=CloseFilled32}}
            on:mouseleave={() => {iconClose=CloseOutline32}}
            on:click={e => dispatch('close')}/>
    </div>
    <h2 class="title">Plugin Selection</h2>
</div> 
<div class="content">
    <div class="tags">
        <Tile>
        <div class="lang">    
                <div>Language</div>
                {#each langTags as item}
                    <Tag category="{item.category}" label={item.label} on:click={()=>addTagFilter(item)}/>
                {/each}
        </div>
        <div class="type">
                <div>Type</div>
                {#each typeTags as item}
                    <Tag category="{item.category}" label={item.label} on:click={()=>addTagFilter(item)} />
                {/each}
        </div>
        <div class="other">
                <div>Other</div>
                {#each otherTags as item}
                    <Tag category="{item.category}" label={item.label} on:click={()=>addTagFilter(item)} />
                {/each}
        </div>
        </Tile>
    </div>
    <div class="search">
        <div class="filter">
            <Search bind:value={pluginNameFilter} placeholder="Search plugin by name ..." size="sm" />
        </div>
        <div class="selectedtags">
            <Tile light>
                <span>Tags:</span>
                {#each pluginTagsFilter as item}
                    <Tag filter category="{item.category}" label='{item.label}' on:click={()=>removeTagFilter(item)}/>
                {/each}
            </Tile>
        </div>
    </div>
    <Tile>
        <Accordion align="start" size="sm">
            {#each filteredPluginlist as item}
                <Plugin plugin={item} display="AccordionItem" on:select />
            {/each}
        </Accordion>
    </Tile>
</div>