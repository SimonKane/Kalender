<template>
  <div class="snippet-wrapper" v-pre>
    <pre class="snippet">
export function FindWhatsMissing() {
    return (
        &lt;div className="tipp-tapp"&gt;
            &lt;Header title="Tomte Badass" /&gt;
            &lt;main&gt;
                &lt;Sidebar items={["Home", "Settings", "Profile"]} /&gt;
                &lt;Content&gt;
                    &lt;Component&gt;
                        {children}
                    &lt;/Component&gt;
                    &lt;Card&gt;
                        &lt;h2&gt;Ho&lt;/h2&gt;
                          &lt;p&gt;
                            This is some random text 
                            about a mafakking toumtenaisse.
                          &lt;/p&gt;
                    &lt;/Card&gt;
                    &lt;Footer&gt;
                &lt;/Content&gt;
            &lt;/main&gt;
        &lt;/div&gt;
    );
}

//Ovan är en komponent
</pre
    >
  </div>
</template>

<style scoped>
:host {
  width: 100%;
  display: block;
}

.snippet-wrapper {
  width: 100%;
  display: block;
  text-align: left;
}

.snippet {
  background: #1e1e1e;
  color: #eaeaea;
  padding: 1.2rem;
  border-radius: 8px;
  font-family: "Fira Code", monospace;
  white-space: pre;
  overflow-x: auto;
  line-height: 1.45;
  font-size: 0.7rem;
}
</style>
