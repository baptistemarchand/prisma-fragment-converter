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

    const convert = () => {
        const a = gql(text)
        const out = convert_(a)
        output = JSON.stringify(out, null, 2)
        console.log(out);
    }
</script>

<button on:click={convert}>CONVERT</button>
<textarea bind:value={text}></textarea>
<pre>
    {output}
</pre>