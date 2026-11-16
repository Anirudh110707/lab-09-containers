
# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.**

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

Anyone who downloads the public image could access my AWS credentials because they would be stored inside the image. Also, the credentials expire when my Learner Lab session ends, so I would have to rebuild the image every time I receive new credentials.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The mounted `.aws` directory gave the container access to my AWS credentials. Since the MongoDB environment variables were not provided, `get_atlas_settings()` used those credentials to retrieve the `mongodb_credentials` parameter from AWS Systems Manager. The container then used those settings to connect to MongoDB Atlas.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

- Before rebuild: `497775619f0e`
- After rebuild: `522f3adabe60`

Using the same tag for a different image is a problem because someone who pulls version 0.1.0 later may receive a different image than before, making it difficult to reproduce the original environment. Instead, I should have used a new version tag, such as 0.1.1, for the updated image.

