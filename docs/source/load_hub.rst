Hugging Face Hub
================

Now that you are all setup, the first step is to load a dataset. The easiest way to load a dataset is from the `Hugging Face Hub <https://huggingface.co/datasets>`_. There are already over 900 datasets in over 100 languages on the Hub. Choose from a wide category of datasets to use for NLP tasks like question answering, summarization, machine translation, and language modeling. For a more in-depth look inside a dataset, use the live `Datasets Viewer <https://huggingface.co/datasets/viewer/>`_.

Load a dataset
--------------

Before you take the time to download a dataset, it is often helpful to quickly get all the relevant information about a dataset. The :func:`datasets.load_dataset_builder` method allows you to inspect the attributes of a dataset without downloading it.

.. code-block::

   >>> from datasets import load_dataset_builder
   >>> dataset_builder = load_dataset_builder('imdb')
   >>> print(dataset_builder.cache_dir)
   /Users/thomwolf/.cache/huggingface/datasets/imdb/plain_text/1.0.0/fdc76b18d5506f14b0646729b8d371880ef1bc48a26d00835a7f3da44004b676
   >>> print(dataset_builder.info.features)
   {'text': Value(dtype='string', id=None), 'label': ClassLabel(num_classes=2, names=['neg', 'pos'], names_file=None, id=None)}
   >>> print(dataset_builder.info.splits)
   {'train': SplitInfo(name='train', num_bytes=33432835, num_examples=25000, dataset_name='imdb'), 'test': SplitInfo(name='test', num_bytes=32650697, num_examples=25000, dataset_name='imdb'), 'unsupervised': SplitInfo(name='unsupervised', num_bytes=67106814, num_examples=50000, dataset_name='imdb')}

.. seealso::

   Take a look at :class:`datasets.DatasetInfo` for a full list of attributes you can use with ``dataset_builder``.

Once you are happy with the dataset you want, load it in a single line with :func:`datasets.load_dataset`:

.. code-block::

   >>> from datasets import load_dataset
   >>> dataset = load_dataset('glue', 'mrpc', split='train')

Select a split
--------------

A split is a specific subset of the dataset like ``train`` and ``test``. Make sure you select a split when you load a dataset. If you don't supply a ``split`` argument, 🤗 Datasets will only return a dictionary containing the subsets of the dataset.

.. code-block::

   >>> from datasets import load_dataset
   >>> datasets = load_dataset('glue', 'mrpc')
   >>> print(datasets)
   {train: Dataset({
       features: ['idx', 'label', 'sentence1', 'sentence2'],
       num_rows: 3668
   })
   validation: Dataset({
       features: ['idx', 'label', 'sentence1', 'sentence2'],
       num_rows: 408
   })
   test: Dataset({
       features: ['idx', 'label', 'sentence1', 'sentence2'],
       num_rows: 1725
   })
   }

Select a configuration
----------------------

Some datasets, like the `General Language Understanding Evaluation (GLUE) <https://huggingface.co/datasets/glue>`_ benchmark, are actually made up of several datasets. These sub-datasets are called **configurations**, and you must explicitly select one when you load the dataset. If you don't provide a configuration name, 🤗 Datasets will raise a ``ValueError`` and remind you to select a configuration.

Use ``get_dataset_config_names`` to retrieve a a list of all the possible configurations available to your dataset:

.. code-block::

   from datasets import get_dataset_config_names

   configs = get_dataset_config_names("glue")
   print(configs)
   # ['cola', 'sst2', 'mrpc', 'qqp', 'stsb', 'mnli', 'mnli_mismatched', 'mnli_matched', 'qnli', 'rte', 'wnli', 'ax']


❌ Incorrect way to load a configuration:

.. code-block::

   >>> from datasets import load_dataset
   >>> dataset = load_dataset('glue')
   ValueError: Config name is missing.
   Please pick one among the available configs: ['cola', 'sst2', 'mrpc', 'qqp', 'stsb', 'mnli', 'mnli_mismatched', 'mnli_matched', 'qnli', 'rte', 'wnli', 'ax']
   Example of usage:
           `load_dataset('glue', 'cola')`

✅ Correct way to load a configuration:

.. code-block::

   >>> dataset = load_dataset('glue', 'sst2')
   Downloading and preparing dataset glue/sst2 (download: 7.09 MiB, generated: 4.81 MiB, total: 11.90 MiB) to /Users/thomwolf/.cache/huggingface/datasets/glue/sst2/1.0.0...
   Downloading: 100%|██████████████████████████████████████████████████████████████| 7.44M/7.44M [00:01<00:00, 7.03MB/s]
   Dataset glue downloaded and prepared to /Users/huggignface/.cache/huggingface/datasets/glue/sst2/1.0.0. Subsequent calls will reuse this data.
   >>> print(dataset)
   {'train': Dataset(schema: {'sentence': 'string', 'label': 'int64', 'idx': 'int32'}, num_rows: 67349),
       'validation': Dataset(schema: {'sentence': 'string', 'label': 'int64', 'idx': 'int32'}, num_rows: 872),
       'test': Dataset(schema: {'sentence': 'string', 'label': 'int64', 'idx': 'int32'}, num_rows: 1821)
   }