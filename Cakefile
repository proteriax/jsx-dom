fs = require('fs-extra')
babel = require('rollup-plugin-babel')
replace = require('@rollup/plugin-replace')
prettier = require('rollup-plugin-prettier')
node = require('@rollup/plugin-node-resolve')
{ rollup } = require('rollup')

extensions = ['.ts', '.js']

build = (name, inject) ->
  try
    bundle = await rollup
      input: './src/index.ts'
      plugins: [
        replace(inject),
        babel(
          extensions: extensions
          comments: false
          minified: false
          plugins: [
            '@babel/plugin-transform-typescript'
            ['@babel/plugin-proposal-object-rest-spread', loose: true, useBuiltIns: true]
            'minify-constant-folding'
            'minify-guarded-expressions'
            'minify-dead-code-elimination'
          ]
        )
        prettier(tabWidth: 2, parser: 'babel'),
        node({extensions})
      ]
    await bundle.write(format: 'cjs', file: "lib/#{name}.cjs.js", exports: "named")
    await bundle.write(format: 'es', file: "lib/#{name}.js", exports: "named")
  catch e
    console.trace()
    console.error(e)

task 'build-slim', 'Build jsx-dom without SVG', ->
  await build('index', __SVG__: false)

task 'build-svg', 'Build jsx-dom with SVG', ->
  await build('svg', __SVG__: true)

task 'build', 'Build everything', ->
  invoke('build-slim')
  invoke('build-svg')

task 'clean', 'Remove built files', ->
  await fs.unlink('lib/index.cjs.js')
  await fs.unlink('lib/index.js')
  await fs.unlink('lib/svg.cjs.js')
  await fs.unlink('lib/svg.js')