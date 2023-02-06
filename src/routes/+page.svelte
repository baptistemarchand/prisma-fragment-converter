<script>
    import gql from 'graphql-tag'

    let output = ''
    let text = ''

    const convert_ = (x) => {
        if (x.kind === 'Document') {
            return convert_(x.definitions[0])
        }
        if (x.kind === 'OperationDefinition') {
            return convert_(x.selectionSet)
        }
        if (x.kind === 'SelectionSet') {
            return Object.fromEntries(x.selections.map(convert_))
        }
        if (x.kind === 'Field') {
            return [x.name.value, x.selectionSet === undefined ? true : {select: convert_(x.selectionSet)}]
        }
        throw Error(`Unknown kind ${x.kind}`)
    }

    const copy = () => {
        navigator.clipboard.writeText(output).then(function() {
            console.log('Async: Copying to clipboard was successful!');
        }, function(err) {
            console.error('Async: Could not copy text: ', err);
        });
    }

    const convert = () => {
        const a = gql(text)
        const out = convert_(a)
        output = JSON.stringify(out, null, 2)
        console.log(out);
    }
</script>

    <textarea bind:value={text} placeholder="copy your prisma 1 fragment here" on:input={convert} rows="60"></textarea>
    <pre>
        {output}
    </pre>
    {#if output}
        <button on:click={copy}>COPY TO CLIPBOARD</button>
    {/if}

<style>
    textarea {
        width: 500px;
        height: 100%;
        float: left;
        border: 1px solid black;
    }
    pre {
        float: left;
        width: 500px;
        margin-left: 20px;
    }
</style>