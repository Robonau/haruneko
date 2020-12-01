
<script lang="ts">
    import {
        Button,
        Search,
        Dropdown,
        InlineLoading,
    } from "carbon-components-svelte";
    import EarthFilled16 from "carbon-icons-svelte/lib/EarthFilled16";
    import { fade } from 'svelte/transition';

    import Chapter from "./Chapter.svelte";

    import type {IManga, IChapter} from '../../../engine/MangaProvider';

    let chapters: Promise<IChapter[]> = Promise.resolve([]);

    export let selectedManga: IManga| null;
    let selectedChapter: IChapter| null;

    //On: MangaChange
    $: {
        chapters = selectedManga ? selectedManga.GetChapters() : Promise.resolve([]);
        selectedChapter = null;
    } 
</script>
<style>
    .Chapter {
        display: grid;
        min-height:0;
        height: 100%;
        grid-template-rows: 2em 2em 2em 1fr;
        gap: 0.3em 0.3em;
        grid-template-areas:
            "ChapterTitle"
            "LanguageFilter"
            "ChapterFilter"
            "ChapterList"
            "ChapterCount";
        grid-area: Chapter;
    }
    .LanguageFilter { grid-area: LanguageFilter; display: table; }
    .ChapterFilter {  grid-area: ChapterFilter; display: table; }
    .ChapterList { grid-area: ChapterList; overflow-y: scroll; overflow-x:hidden}
    .ChapterCount { grid-area: ChapterCount; margin:0.25em;}

    .separator {
        border-bottom: var(--manga-control-separator);
    }

    .inline{
        width:fit-content;
    }
    .inline-wide{
        display:table-cell;
        width:100%;
    }
</style>
<div class="Chapter" transition:fade>
    <div class="ChapterTitle">
        <h5 class="separator">Chapter List</h5>
    </div>
    <div class="LanguageFilter">
        <div class="inline">
        <Button 
            icon={EarthFilled16}
            hasIconOnly
            size="small"
            tooltipPosition="bottom"
            tooltipAlignment="center"
            iconDescription="Plugin"
        />
        </div>
        <div class="inline-wide">
            <Dropdown
                selectedIndex={0}
                size="sm"
                items={[{ id: '0', text: '*' }, { id: '1', text: 'gb' }, { id: '2', text: 'fr' }]}
                
            />
        </div>
    </div>
    <div class="ChapterFilter">
        <Search size="sm"/>
    </div>
    <div class="ChapterList list">
        {#await chapters}
            <InlineLoading status="active" description="Working..." />
        {:then chapters}
            {#each chapters as chapter, i}
                <Chapter chapter={chapter} selected={selectedChapter===chapter} on:select={e => selectedChapter = e.detail}/>
            {/each}
        {:catch error}
            <p style="color: red">{error.message}</p>
        {/await}
    </div>
    <div class="ChapterCount">
        {#await chapters}
            Chapters: ?
        {:then chapters}
            Chapters: {chapters.length}
        {:catch error}
            Chapters: ?
        {/await}
    </div>
</div>