def fill_empty_post_params(input_file, output_file, dummy_value="test123"):
    """
    Reads a raw HTTP POST request, fills empty parameters with a dummy value,
    and writes the modified request to a new file.
    """
    with open(input_file, 'r') as f:
        lines = f.read().splitlines()

    headers = []
    post_data = []
    is_body = False

    # Separate headers and body
    for line in lines:
        if line.strip() == '':
            is_body = True
            continue
        if not is_body:
            headers.append(line)
        else:
            post_data.append(line)

    # Process POST body
    if post_data:
        body_line = post_data[0]
        params = body_line.split("&")
        filled_params = []

        for param in params:
            if '=' in param:
                key, value = param.split("=", 1)
                if value.strip() == "":
                    value = dummy_value
                filled_params.append(f"{key}={value}")
            else:
                # Handle case where param is like "param" with no "=" at all
                filled_params.append(f"{param}={dummy_value}")

        new_body = "&".join(filled_params)
        full_request = "\n".join(headers + [''] + [new_body])

        # Save to file
        with open(output_file, 'w') as out:
            out.write(full_request)

        print(f"\n Modified request saved to: {output_file}")
        print("\n Modified Request:\n" + full_request)
    else:
        print(" No POST body found in the request.")


# === Run the function ===
if __name__ == "__main__":
    fill_empty_post_params("request.txt", "request_filled.txt", dummy_value="test123")
