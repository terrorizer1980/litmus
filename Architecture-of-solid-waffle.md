# Architecture of solid-waffle

Solid-waffle tries to use existing technologies as much as possible. It relies heavily on bolt and serverspec.

## technology stack

## provision
rake task -> bolt -> waffle-provision -> waffle-image -> docker
                                      -> abs (internal)
                                      -> vmpooler

## install an agent

rake task -> bolt -> puppet-agent

## install module

rake task -> pdk -> bolt

## run tests

rake task -> serverspec -> rspec

## tear down

rake task -> bolt -> waffle-provision -> waffle-image -> docker
                                      -> abs (internal)
                                      -> vmpooler