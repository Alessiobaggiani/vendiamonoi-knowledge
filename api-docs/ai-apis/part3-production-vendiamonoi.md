                    {
                        "role": "user",
                        "content": chunk
                    }
                ]
            )
            
            # Step 4: Parse response
            response_text = response.content[0].text
            
            try:
                products = json.loads(response_text)
                all_products.extend(products)
            except json.JSONDecodeError:
                print(f"  Warning: Could not parse JSON from chunk {chunk_idx}")
                continue
        
        # Step 5: Valida con Pydantic
        validated_products = []
        validation_errors = []
        
        for product in all_products:
            try:
                validated = ProductDataSchema(**product)
                validated_products.append(validated)
            except ValidationError as e:
                validation_errors.append({
                    "sku": product.get("sku"),
                    "errors": [str(err) for err in e.errors()]
                })
        
        print(f"\nExtraction complete:")
        print(f"  Total extracted: {len(all_products)}")
        print(f"  Valid: {len(validated_products)}")
        print(f"  Validation errors: {len(validation_errors)}")
        
        # Step 6: Save to Supabase
        await self._save_to_supabase(validated_products)
        
        return validated_products
    
    def _extract_text_from_pdf(self, pdf_path: str) -> str:
        """Extract text from PDF"""
        doc = fitz.open(pdf_path)
        text = ""
        for page_num in range(len(doc)):
            page = doc[page_num]
            text += f"\n--- PAGE {page_num + 1} ---\n"
            text += page.get_text()
        return text