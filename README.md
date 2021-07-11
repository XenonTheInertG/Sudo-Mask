# Sudo-Mask
Fake sudo mask buildpack for heroku(Heroku Root buildpack)
- As we all know heroku doesn't let users to use Sudo commands or they don't have a support for sudo. Recently i was making a bot & deployed it to heroku but the problem was the needs root. I searched in google & found Fabioking's fakechroot buildpack. Tried to install it but it was broken af. It was a buildpack from 2013,so i made my own buildpack:)
A Heroku Buildpack that enables root permisions using chroot jail

# Applying the buildpack

--------------------------------------------------
NOTE: Make sure you are logged in with heroku CLI
--------------------------------------------------

$ heroku create --buildpack https://github.com/XenonTheInertG/Sudo-Mask.git

- Copy this code to create a heroku app with this buildpack

$ heroku buildpacks:add https://github.com/XenonTheInertG/Sudo-Mask.git

- And this is to add to existing app and make sure you are connected to the app if not make sure you are by

$ heroku git:remote -a (app-name-here)

# Contributors

This buildpack is based on [fakesu](https://github.com/fabiokung/heroku-buildpack-fakesu)
buildpack by @fabiokung. buildpack by @fabiokung and chroot+aptitude heroku buildpack by [Gabriel Falcão](https://github.com/gabrielfalcao)
