
This page documents all the things that we hope to do to make FauxPilot better.
Basically,
if I had infinite free time,
    this is what I would do,
and this maybe will help give other contributors an idea of things they could work on.
If you've got something you want to add to this list,
please open an issue to discuss it!

If you want to try to tackle one of these wishlist items,
please open an issue to track your progress and
add a link to the issue on this page.

## Documentation

- A list of supported GPUs and models known to work with FauxPilot (initial page here: [[GPU Support Matrix|GPU-Support-Matrix]])

- A troubleshooting FAQ/guide to help people diagnose the sometimes cryptic errors that Triton and
    the NVIDIA container throw (usually caused by something like CUDA/NVIDIA driver version issues).

- A bit more documentation on how to get the official Copilot VSCode extension to talk to FauxPilot,
including discussion of how to update the tokenizer it uses 
to use the CodeGen tokenizer instead
(which is still not fully tested but should fix issues with Copilot requesting prompts that don't actually fit into the models' context window).

- Some documentation/discussion of how to fine-tune a model on your own code dataset and
    then use it with Copilot
    (@shailja-thakur has done this successfully and
    has some patches that will be merged soon to make the process smoother).

- Links to projects that are useful for folks running FauxPilot,
    like the [vscode-fauxpilot plugin](https://github.com/Venthe/vscode-fauxpilot)
    (and any other similar extensions for other IDEs).

- Possibly some kind of discussion or Q&A type of forum so that
    people don't have to open issues just to ask questions?
    This might also be a good place to announce things related to the project.

- Some developer-oriented documentation:
    how to build the necessary Docker images
    (and where the Dockerfiles for them can be found), how to submit PRs, etc.

## Testing/Maintenance

- Basic CI/CD to rebuild the Docker images and push them to Docker Hub automatically when they get updated.

- Basic test suite:
  - Test to make sure at least the smallest model
      (currently CodeGen-350M) works and can generate some output.
      This may be tricky to do in GH Actions since
      we need GPU access to test a real model;
      there is a rodamap page that suggests this feature is planned
      (and maybe already available?):
          https://github.com/github/roadmap/issues/505

  - Unit tests for the proxy parts.
      Here we can mock out responses from the inference server,  I think?
      But we'd probably also want something to make sure the mock data stays in sync with what Triton actually returns.

  - Some tests that try to exercise the larger models as well,
      which can be run locally periodically to make sure everything is still working.

- Performance benchmarking scripts:
    how fast can we generate new tokens on various GPUs/models?
    Making it easy to check this will help test for performance regressions.

- Syncing with upstream FT and Triton,
    and contributing back fixes and changes we've made locally.

## Features

- ~Support for HuggingFace+DeepSpeed models.
    Apparently,
    these have [gotten very fast](https://huggingface.co/blog/bloom-inference-pytorch-scripts),
    and many models also support things that can cut the memory requirements in half like [INT8 quantization](https://huggingface.co/blog/hf-bitsandbytes-integration) (more [in-depth post here](https://timdettmers.com/2022/08/17/llm-int8-and-emergent-features/)). Triton supports a [Python backend that can be used to run HF models](https://github.com/triton-inference-server/server/issues/2747),
    but it seems to be poorly documented right now.~ 
    -- we have a python backend now.

- More models!
    Once we have an HF backend,
    we can add a bunch of new and exciting code models beyond the existing Salesforce CodeGen ones that will
    give some cool capabilities,
    like:
  - [Polycoder](https://github.com/VHellendoorn/Code-LMs) - a code model "trained on a [large corpus](https://github.com/VHellendoorn/Code-LMs#data-characteristics) of code spanning 12 programming languages.
      This includes a 2.7B parameter model
          (nick-named PolyCoder, trained for 100K and 150K steps)."

  - [Facebook Incoder](https://huggingface.co/facebook/incoder-1B) - Adds the ability to 
        consider program context both before *and* after the cursor,  and fill in the middle.

  - [CodeParrot](https://huggingface.co/codeparrot/codeparrot) - A code model trained by HuggingFace on Python code

- Some features to make it possible to deploy FauxPilot in production and
    scale it up to more than just a single local user.
    Most basic scaling feature:
        the ability to run multiple Triton instances on the backend with the FauxPilot proxy load-balancing between them.
        More advanced/complex features:
            batching together multiple requests that come in at the same time
            (and possibly even choosing batches of similar lengths which
            helps improve GPU perf)
            - this could be tricky to do while satisfying some latency constraints.

- Token streaming.
    OpenAI's API allows tokens to be streamed to the user as they're generated.
    We currently fake this by
    just generating the whole completion and
        returning it all at once.
    Making it stream results as they come in would be nice for some kinds of interactive use.
    This could have negative effects on latency though,
        which would have to be evaluated.
    FasterTransformer backend does [seem to support this](https://github.com/triton-inference-server/fastertransformer_backend/blob/225b57898b830a13b5634ee10b812c96bad802b0/docs/gptj_guide.md#decoupled-mode).

- Logits/log-probabilities for the top K tokens.
    Again this is supported by OpenAI's API,
        but FasterTransformer only provides the probability of the top token.

    This one isn't too hard to add (I think)
        but needs changes to the FT codebase.
    The official Copilot VSCode plugin uses 
                                            the difference in the probabilities of the top two tokens 
                                        to get a measure of how confident it is in a given suggestion;
        we currently just make up a fake probability for everything except the top token.

- Mechanism for cancelling in-flight requests.
    In the context of an IDE plugin,
    there may be many requests made that become irrelevant because the user keeps typing;
    it would be great to have a way to cancel a request that's in progress so we don't waste the GPU time.

- Some kind of **opt-in** logging/analytics/telemetry to support things like research studies using FauxPilot.
