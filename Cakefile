fs            = require 'fs'
glob          = require 'glob'
path          = require 'path'
DataURI       = require 'datauri'
{exec, spawn} = require 'child_process'

# Utilities
# ------------------------------------------------------------------------------

# ANSI Terminal Colors
bold = '\x1b[0;1m'
green = '\x1b[0;32m'
reset = '\x1b[0m'
red = '\x1b[0;31m'

log = (message, color, explanation) -> console.log color + message + reset + ' ' + (explanation or '')

# Functions
# ------------------------------------------------------------------------------

filenameFromConfigFilename = (configFilename) ->
  configFilename
    .replace(/config-/, '')
    .replace(/src\//, 'templates/')
    .replace(/json$/, 'js')

imageToDataURI = (image) ->
  if fs.existsSync(image)
    imageData = new DataURI(image)
    return imageData.content
  else
    return image

build = (template, configFile) ->
  filename = filenameFromConfigFilename(configFile)
  log 'build', green, 'Using ' + bold + configFile + reset + ' to generate ' + bold + filename + reset

  config = JSON.parse(fs.readFileSync(configFile).toString())

  # convert image paths in config to Data URI
  for k, v of config when k.match(/image(1x|2x)?$/i) and v.length > 0 and !v.match(/^(data|https?):/)
    image = path.join(path.dirname(configFile), v)
    config[k] = imageToDataURI(image)

  result = template.replace(/<%\s*template_defaults\s*%>/, JSON.stringify(config, null, '\t'))
  log "Writing file #{filename}", bold
  fs.writeFileSync(filename, result)
  log "File written #{filename}", bold

# Tasks
# ------------------------------------------------------------------------------

task 'clean', 'Clean existing template files', ->
  log 'clean', green, 'Cleaning out templates directory...'
  exec 'rm -rf templates/*'

task 'build', 'Build template files', ->
  invoke 'clean'

  template = fs.readFileSync('src/template.js').toString()

  glob 'src/config-*.json', (err, files) ->
    build(template, configFile) for configFile in files
    log '\nAll done. Have a nice day!', bold